pipeline {
  agent none
  stages {
    //Build container image
//     stage('Build') {
//       agent {
//         kubernetes {
//           label 'jenkinsrun'
//           defaultContainer 'dind'
//           yaml """
// apiVersion: v1
// kind: Pod
// spec:
//   containers:
//   - name: dind
//     image: docker:18.05-dind
//     securityContext:
//       privileged: true
//     volumeMounts:
//       - name: dind-storage
//         mountPath: /var/lib/docker
//   volumes:
//     - name: dind-storage
//       emptyDir: {}
// """
//         }
//       }
//       steps {
//         sh "printenv"
//         container('dind') {
//           script {
//             docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-auth') {
//               def pathTag = "raleonid/app-meow:${JOB_BASE_NAME}"
//               if(env.TAG_NAME == null || env.TAG_NAME.length() == 0) {
//                 pathTag = "${pathTag}-${BUILD_ID}"
//               }
//               //build the image
//               def customImage = docker.build(pathTag)
//               //upload it to the registry
//               customImage.push()
//               println "test5"
//             }
//           }
//         }
//       }
//     }
    stage('Deploy') {
      agent {

        kubernetes {
          label 'helm-pod'
          yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: helm
    image: wardviaene/helm-s3
    command:
    - cat
    tty: true
"""
         }
      }
      when {
          // Only say hello if a "greeting" is requested
          expression { env.TAG_NAME != null && env.TAG_NAME.length() > 0 }
      }
      steps {
        container('helm') {
          sh "helm template deploy -n stage --set image.tag=${TAG_NAME} > deployment.yaml"
          sh "readlink -f deployment.yaml"
        }
      }
    }
    stage('Deploy1') {
       when {
          // Only say hello if a "greeting" is requested
          expression { env.TAG_NAME != null && env.TAG_NAME.length() > 0 }
      }
      steps {
        kubernetesDeploy(configs: "deployment.yaml")
      }
    }
  }
}
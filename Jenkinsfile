// pipeline {

//   environment {
//     dockerimagenameBE = "nhutlinh231/backend-k8s"
//     dockerimagenameFE = "nhutlinh231/frontend-k8s"
//     dockerImageBE = ""
//     dockerImageFE = ""

//     SONARQUBE_ENV = 'SonarCloud'
//     SONAR_PROJECT_KEY = 'nhutlin_Instagram-mern-CICD'
//     SONAR_ORGANIZATION = 'NhutLinh'
//     SONAR_TOKEN = credentials('633c42fe50509b7d8f8f81cb9f03df23cb8c6524')

//   }

//   agent any

//   tools {
//     nodejs '20.13.1'
//   }

//   stages {

//     stage('Checkout Source') {
//       steps {
//         git branch: 'main', url: 'https://github.com/nhutlin/Instagram-mern-CICD.git'
//       }
//     }

//     stage('Build') {
//       steps {
//         sh 'npm -v'
//         sh 'cd /var/lib/jenkins/workspace/Instagram-CICD/frontend && npm install && npm run build'
//         sh 'cd /var/lib/jenkins/workspace/Instagram-CICD && npm install'
//         echo 'Run build successfully...'
//       }
//     }

//     // stage('Test') {
//     //   steps {
//     //     sh 'cd /var/lib/jenkins/workspace/Instagram-CICD/frontend && npm run test'
//     //     echo 'Run test successfully...'
//     //   }
//     // }
    // stage('SonarCloud Analysis') {
    //   steps {
    //     withSonarQubeEnv('SonarCloud') {
    //       sh "sonar-scanner \
    //       -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} \
    //       -Dsonar.organization=${env.SONAR_ORGANIZATION} \
    //       -Dsonar.sources=. \
    //       -Dsonar.host.url=https://sonarcloud.io \
    //       -Dsonar.login=${env.SONAR_TOKEN}"
    //     }
    //   }
    // }
        
    // stage("Quality Gate") {
    //   steps {
    //     timeout(time: 1, unit: 'HOURS') {
    //       waitForQualityGate abortPipeline: false
    //     }
    //   }
    // }


//     stage('Build image') {
//       steps{
//         script {
//           dockerImageBE = docker.build dockerimagenameBE
//           // sh 'cd frontend/'
//           // dockerImageFE = docker.build dockerimagenameFE
//           // sh 'docker images'
//           dockerImageFE = docker.build(dockerimagenameFE, 'frontend/.')
          
//         }
//       }
//     }

//     stage('Pushing Image') {
//       environment {
//                registryCredential = 'dockerhublogin'
//            }
//       steps{
//         script {
//           docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {

//           dockerImageFE.push('latest')
//           dockerImageBE.push('latest')


//          }

//         }
//       }
//     }
//   //   stage('Deploy to K8s'){
//   //     steps{
//   //       script{
//   //         dir('Kubernetes') {
//   //           withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: 'kubernetes-admin@kubernetes', credentialsId: 'kubeconfig', namespace: 'default', restrictKubeConfigAccess: false) {
//   //             sh 'kubectl apply -f /var/lib/jenkins/workspace/Instagram-CICD/deployment.yml'
//   //             sh 'kubectl get all'
//   //           } 
//   //         }
//   //       }
//   //     }
//   //   }
//   // }
//   }
//   post {
//         always {
//             script {
//                 if (getContext(hudson.FilePath)) {
//                     deleteDir()
//                 }
//             }
//         }
    
//     }
  
// }

pipeline {

  environment {
    dockerimagenameFE = "nhutlinh231/frontend-k8s"
    dockerImageFE = ""
    dockerimagenameBE = "nhutlinh231/backend-k8s"
    dockerImageBE = ""

    SONARQUBE_ENV = 'SonarCloud'
    SONAR_PROJECT_KEY = 'nhutlin_Instagram-mern-CICD'
    SONAR_ORGANIZATION = 'NhutLinh'
    SONAR_TOKEN = credentials('633c42fe50509b7d8f8f81cb9f03df23cb8c6524')
  }

  agent any

  tools {
    nodejs '20.13.1'
  }

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/nhutlin/Instagram-mern-CICD.git'
      }
    }

    // stage('Build') {
    //   steps {
    //     sh 'npm version'
    //     sh 'cd /var/lib/jenkins/workspace/Instagram-CICD && npm install'
    //     sh 'cd /var/lib/jenkins/workspace/Instagram-CICD/frontend && npm install'
    //     sh 'cd /var/lib/jenkins/workspace/Instagram-CICD/frontend && npm run build'

    //     echo 'Install npm successfully...'
    //   }
    // }

    stage('Build image') {
      steps{
        script {
          dockerImageBE = docker.build(dockerimagenameBE, '.')
          dockerImageFE = docker.build(dockerImagenameFE, 'frontend/.')
        }
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        withSonarQubeEnv('SonarCloud') {
          sh "sonar-scanner \
          -Dsonar.projectKey= \
          -Dsonar.organization=${env.SONAR_ORGANIZATION} \
          -Dsonar.sources=. \
          -Dsonar.host.url=https://sonarcloud.io \
          -Dsonar.login=${env.SONAR_TOKEN}"
        }
      }
    }
        
    stage("Quality Gate") {
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: false
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImageFE.push("latest")
            dockerImageBE.push("latest")

          }
        }

      }
    }

    // stage('Deploying App to Kubernetes') {
    //   steps {
    //     script {
    //       kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "kubernetes")
    //     }
    //   }
    // }

    // stage('Deploy to K8s'){
    //   steps{
    //     script{
    //       dir('Kubernetes') {
    //         withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: 'kubernetes-admin@kubernetes', credentialsId: 'kubeconfig', namespace: 'default', restrictKubeConfigAccess: false) {
    //           sh 'kubectl apply -f /var/lib/jenkins/workspace/Github-BE-Instagram/deployment.yml'
    //           sh 'kubectl get all'
    //         } 
    //       }
    //     }
    //   }
    // }
  }
  
  // post {
  //       always {
  //           // Clean up docker images
  //           cleanWs()
  //           sh "docker image prune -f"
  //       }
  //   }

}
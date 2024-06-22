pipeline {

  environment {
    dockerimagenameFE = "nhutlinh231/frontend-k8s"
    dockerImageFE = ""
    dockerimagenameBE = "nhutlinh231/backend-k8s"
    dockerImageBE = ""
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

    stage('Build image') {
      steps{
        script {
          sh 'npm --version && node --version'
          dockerImageBE = docker.build(dockerimagenameBE, '.')
          dockerImageFE = docker.build(dockerImagenameFE, 'frontend/.')
        }
      }
    }

    // SonarCloud (optional)
    // stage('SonarCloud Analysis') {
    //   steps {
    //     withSonarQubeEnv('SonarCloud') {
    //       sh "sonar-scanner \
    //       -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} \
    //       -Dsonar.organization=${env.SONAR_ORGANIZATION} \
    //       -Dsonar.sources=. \
    //       -Dsonar.host.url=https://sonarcloud.io \
    //       "
    //     }
    //   }
    // }
    
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQubeBE') {
          sh "sonar-scanner \
          -Dsonar.projectKey=InstagramBECICD \
          -Dsonar.sources=. \
          -Dsonar.host.url=http://192.168.30.113:9000 \
          "
        }
        withSonarQubeEnv('SonarQubeFE') {
          sh "sonar-scanner \
          -Dsonar.projectKey=InstagramFECICD \
          -Dsonar.sources=frontend/. \
          -Dsonar.host.url=http://192.168.30.113:9000 \
          "
        }
      }
    }
        
    stage("Quality Gate") {
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: true
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

    stage('Deploy to K8s'){
      steps{
        script{
          dir('Kubernetes') {
            withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: 'kubernetes-admin@kubernetes', credentialsId: 'kubeconfig', namespace: 'default', restrictKubeConfigAccess: false) {
              sh 'kubectl apply -f /var/lib/jenkins/workspace/Instagram-CICD/deployment.yml'
              sh 'kubectl get all'
            } 
          }
        }
      }
    }
  }
  
  post {
        always {
            // Clean up docker all images
            cleanWs()
            sh "docker image prune -a"
        }
    }
}
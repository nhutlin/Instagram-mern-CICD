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
    
    stage('SonarQube Analysis Backend') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh "sonar-scanner \
          -Dsonar.projectKey=InstagramBECICD \
          -Dsonar.sources=. \
          -Dsonar.host.url=http://192.168.30.113:9000 \
          -Dsonar.token=sqp_948683ec986fa9d9859557750a931ea9a2af76ee \
          "
          sh "sonar-scanner \
          -Dsonar.projectKey=InstagramFECICD \
          -Dsonar.sources=. \
          -Dsonar.host.url=http://192.168.30.113:9000 \
          -Dsonar.token=sqp_526590027228f40a764a706099c918cf34d08111 \
          "
        }
      }
    }

    // stage('SonarQube Analysis Frontend') {
    //   steps {
    //     withSonarQubeEnv('SonarQube') {
    //       sh "sonar-scanner \
    //       -Dsonar.projectKey=${env.SONARQUBE_PROJECT_KEY_FE} \
    //       -Dsonar.sources=frontend/. \
    //       -Dsonar.host.url=http://192.168.30.113:9000 \
    //       -Dsonar.token=sqp_264bbe7443791e9a16b8deda8eb1662de2a3d943 \
    //       "
    //     }
    //   }
    // }
        
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
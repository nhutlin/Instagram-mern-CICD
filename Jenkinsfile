pipeline {

  environment {
    dockerimagenameBE = "nhutlinh231/backend-k8s"
    dockerimagenameFE = "nhutlinh231/frontend-k8s"
    dockerImageBE = ""
    dockerImageFE = ""

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

    stage('Build') {
      steps {
        sh 'npm -v'
        sh 'cd /var/lib/jenkins/workspace/Instagram-CICD/frontend && npm install && npm run build'
        sh 'cd /var/lib/jenkins/workspace/Instagram-CICD && npm install'
        echo 'Run build successfully...'
      }
    }

    stage('Test') {
      steps {
        sh 'cd /var/lib/jenkins/workspace/Instagram-CICD/frontend && npm run test'
        echo 'Run test successfully...'
      }
    }


    stage('Build image') {
      steps{
        script {
          dockerImageBE = docker.build dockerimagenameBE
          sh 'cd frontend/'
          dockerImageFE = docker.build dockerimagenameFE
          
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
            // Clean up docker images
      cleanWs()
      sh "docker image prune -f"
    }
  }
}
  



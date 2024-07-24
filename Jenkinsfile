pipeline {
  environment {
    dockerimagename = "theidiothuy45/web-thingy"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/Jackhuy1/web-nginx.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Pushing Image') {
      environment {
          registryCredential = 'dockerhub-login'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("v1")
          }
        }
      }
    }
    stage('Deploy App to kuberentes') {
      steps {
         container('kubectl') {
          withCredentials([file(kube-login: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" deployment.yaml'
            sh 'kubectl apply -f deployment.yaml'
        }
      }
    }
  }
}

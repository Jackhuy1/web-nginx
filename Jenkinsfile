pipeline {
  environment {
    dockerimagename = "theidiothuy45/webthingy"
    dockerImage = ""
  }
agent {
        kubernetes {
            label 'kube-agent'
            yaml """
apiVersion: v1
kind: Pod
spec:
containers:
- name: docker-client
  image: docker:19.03.1
  command: ['sleep', '99d']
  env:
    - name: DOCKER_HOST
      value: tcp://localhost:2375
- name: docker-daemon
  image: docker:19.03.1-dind
  env:
    - name: DOCKER_TLS_CERTDIR
      value: ""
  securityContext:
    privileged: true
  volumeMounts:
      - name: cache
        mountPath: /var/lib/docker
volumes:
  - name: cache
    hostPath:
      path: /tmp
      type: Directory
"""
    }
  }

  stages {
  stage('Docker Build') {
            steps {
                container('docker-client') {
                    sh 'docker version && DOCKER_BUILDKIT=1 docker build --progress plain -t web-nginx:v1 .'
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
    stage('Deploy App to Kubernetes') {    
      steps {
        container('kubectl') {
          withCredentials([file(credentialsId: 'kube-login', variable: 'KUBECONFIG')]) {
            sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" deployment.yaml'
            sh 'kubectl apply -f deployment.yaml'
          }
        }
      }
    }
  }
}

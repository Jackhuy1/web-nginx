pipeline {
    agent {
        kubernetes {
            label 'docker-kube'
            yaml """
                apiVersion: v1
                kind: Pod
                spec:
                containers:
                - name: docker
                  image: docker:latest
                  command: ['sleep', '99d']
                  env:
                    - name: DOCKER_HOST
                      value: tcp://localhost:2375
                - name: docker-daemon
                  image: docker:latest-dind
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
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Jackhuy1/web-nginx.git'
            }
        }
        stage('Docker Build') {
            steps {
                container('docker-client') {
                    sh 'docker version && DOCKER_BUILDKIT=1 docker build --progress plain -t web-nginx:v1 .'
                }
            }
        }
    }
}

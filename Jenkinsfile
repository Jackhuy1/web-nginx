podTemplate(yaml: """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:1.11
    command: ['cat']
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
  ) {

  def image = "web-nginx"
  node(POD_LABEL) {
    stage('Build Docker image') {
       git branch: 'main', 
            url: 'https://github.com/Jackhuy1/web-nginx.git'
      container('docker') {
        sh "docker build -t ${image} ."
      }
    }
  }
}

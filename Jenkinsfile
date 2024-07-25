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

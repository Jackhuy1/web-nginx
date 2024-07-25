podTemplate(label: 'kube-agent', containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent:latest', args: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins', resourceRequestCpu: '500m', resourceLimitCpu: '500m', resourceRequestMemory: '1024Mi', resourceLimitMemory: '1024Mi'),
    containerTemplate(name: 'docker', image: 'docker:latest', command: 'cat', ttyEnabled: true, privileged: true),
],
volumes:[
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
]){

  node ('kube-agent') {

    def pwd = pwd()
    def tags = [env.BUILD_TAG, 'latest']
    def docker_registry_url = "index.docker.io"
    def docker_repo = "web-thingy"
    def docker_acct = "theidiothuy45"
    // checkout sources
    checkout scm
    // Build and push the Docker image
    stage ('Build & Push Docker image') {

      container('docker') {
        println "build & push"

        // perform docker login
	withCredentials([usernamePassword(credentialsId: 'dockerhub-login', passwordVariable: 'password', usernameVariable: 'username')]){
                         sh '''
                            echo "${password} | docker login -u ${username} --password-stdin"
                         '''
        }

        // build and publish container
        pipeline.containerBuildPub(
            dockerfile: "./",
            host      : docker_registry_url,
            acct      : docker_acct,
            repo      : docker_repo,
            tags      : tags,
            auth_id   : dockerhub-login
        )
      }
    }
  }
}

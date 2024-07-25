#!/usr/bin/groovy

podTemplate(label: 'jenkins-agent', containers: [
    containerTemplate(name: 'jnlp', image: 'jenkinsci/jnlp-slave:2.62', args: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins', resourceRequestCpu: '500m', resourceLimitCpu: '500m', resourceRequestMemory: '1024Mi', resourceLimitMemory: '1024Mi'),
    containerTemplate(name: 'docker', image: 'docker:1.12.6', command: 'cat', ttyEnabled: true),
],
volumes:[
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
]){

  node ('jenkins-agent') {

    def pwd = pwd()
    def tags = [env.BUILD_TAG, 'latest']
    def docker_registry_url = "index.docker.io"
    def docker_email = "huyluong41@gmail.com"
    def docker_repo = "web-thingy"
    def docker_acct = "theidiothuy45"

    // Build and push the Docker image
    stage ('Build & Push Docker image') {

      container('docker') {
        println "build & push"

        // perform docker login
        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:dockerhub-login , usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
          sh "docker login -e ${docker_email} -u ${env.USERNAME} -p ${env.PASSWORD} ${docker_registry_url}"
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

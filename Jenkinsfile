#!/usr/bin/groovy

def utils = new com.activedisclosure.Utils()

podTemplate(label: 'jenkins-pipeline', nodeSelector: 'Role=Application.Linux', containers : [
    containerTemplate(name: 'jnlp', image: 'jenkinsci/jnlp-slave:3.14-1-alpine', orgs: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins', resourceRequestCpu: '200m', resourceLimitCpu: '200m', resourceRequestMemory: '256Mi', resourceLimitMemory: '256Mi'),
    containerTemplate(name: 'docker', image: 'docker:17.10.0', command: 'cat', ttyEnabled: true)
],
volumes:[
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
]){

    node('jenkins-pipeline') {

      def pwd = pwd()

      stage('checkout code and setup Git env vars') {
        checkout scm
        utils.gitEnvVars()
        println "Printing All Environment Values"
        echo sh(returnStdout: true, script: 'env')
      }

      // read in required jenkins workflow config values
      def Map config = parseJson('Jenkinsfile.json')
      println "pipeline config ==> ${config}"

      // continue only if pipeline enabled
      if (!config.pipeline.enabled) {
          println "pipeline disabled"
          return
      }

    def acct = utils.getContainerRepoAcct(config)
    def tags = utils.getContainerTags(config)

    stage ('build container') {
      dockerBuildAndPublish(config, tags, acct)
    }
  }
}
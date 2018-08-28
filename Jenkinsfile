#!/usr/bin/groovy
def label = buildId()
def alpineVersion = '3.7'

linuxPodTemplate(name: 'alpine-build', image: "alpine:${alpineVersion}") {
  node(label) {
    def utils = new com.activedisclosure.Utils()
    stage('checkout code and setup Git env vars') {
      checkout scm
      utils.gitEnvVars()
      println "Printing All Environment Values"
      echo sh(returnStdout: true, script: 'env')
    }

    // read in required jenkins workflow config values
    def Map config = parseJson('Jenkinsfile.json')
    println "pipeline config ==> ${config}"

    def acct = utils.getContainerRepoAcct(config)
    def tags = utils.getContainerTags(config)

    stage ('build container') {
      dockerBuildAndPublish(config, tags, acct)
    }
  }
}
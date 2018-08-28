def utils = new com.activedisclosure.Utils()

k8s-kubectlTemplate {
  def buildPath = "/home/jenkins/go/src/github.com/dfs-activedisclosure/k8s-kubectl"
  dir(buildPath) {
    stage('checkout code and setup Git env vars') {
      checkout scm

      utils.gitEnvVars()

      println "Printing All Environment Values"
      echo sh(returnStdout: true, script: 'env')
    }

    // Parses the JSON config into a Map and prints the Map
    def Map config = parseJson('Jenkinsfile.json')
    println "pipeline config ==> ${config}"

    def acct  = utils.getContainerRepoAcct(config)
    def tags  = utils.getContainerTags(config)

    stage('docker build and publish') {
      dockerBuildAndPublish(config, tags, acct)
    }
  }
}

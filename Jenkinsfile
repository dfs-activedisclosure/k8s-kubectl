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
    def version_tag = tags.get(0)

    stage('docker build and publish') {
      dockerBuildAndPublish(config, tags, acct)
    }

    approval {
      message = 'Test'
      okText = 'Valid test?'
      timeoutTime = 30      
    }

    if (utils.isMasterBranch()) {
      stage('approve to production') {
        approval {
          message = 'Deploy to production?'
          okText = 'Deploy'
          timeoutTime = 10
        }
      }

      stage('deploy to production') {
        standardRelease(config, version_tag) {}
      }
    }
  }
}

pipeline {
  agent { label 'master' }
  environment {
    DOCKER_RELEASE_TAG=''
    DOCKER_RELEASE_TAG_RISKDB=''
    branch_name=''
    projectKey=''
    projectName=''
    
  }
  tools {
    maven 'Maven 3.5.4'
  }
    options {
    timestamps()
    timeout(time: 30)
   // skipDefaultCheckout()
    skipStagesAfterUnstable()
  }
  stages {
stage('Sonarqube-MR') {
   environment {
        scannerHome = tool name: 'RSSONAR', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }
    steps {
        script {
            def BRANCH = sh(returnStdout: true, script: 'echo /"/$/{env.GIT_BRANCH/}"').trim()
            BRANCH_NAME=BRANCH
            def reponame = sh(returnStdout: true, script: 'echo /"/$/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/risksense/', '').replaceAll('.git', '')
            PROJECTKEY=reponame
            PROJECTNAME=reponame
        }
        sh "printenv | sort"
    }
}
  }
}    

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
    when {
        expression {
    return !(GIT_BRANCH ==~ /^PR-\d+$/)
        }
    beforeAgent true

        }
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
stage('SONARQUBE-PR') {
        when {
        expression {
    return (GIT_BRANCH ==~ /^PR-\d+$/)
        }
    beforeAgent true
        }
   environment {
        scannerHome = tool name: 'RSSONAR', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }
    steps {
        script {
            def reponame = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/risksense/', '').replaceAll('.git', '')
            PROJECTKEY=reponame
            PROJECTNAME=reponame
            def org = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/', '').replaceAll('.git', '')
            ORG_NAME=org
        }        
        sh "printenv | sort"
        sh "echo ${env.GIT_URL}"
        sh "echo ${env.GIT_BRANCH}"
        sh 'echo "PR sonar build"'
        gitHubPRStatus githubPRMessage('Qualitygate Scan started')
    }
    post {
    success {
          script {
    def reponame = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/risksense/', '').replaceAll('.git', '')
    PROJECTKEY=reponame
    PROJECTNAME=reponame
    def org = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/', '').replaceAll('.git', '')
    ORG_NAME=org
    }

      githubNotify account: 'kiransre', context: 'QualityGate', credentialsId: 'Github', description: 'Qualitygate is finished', gitApiUrl: '', repo: "$ORG_NAME", sha: "${env.GIT_COMMIT}", status: 'SUCCESS', targetUrl: ''
    }
    failure {
          script {
    def reponame = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/risksense/', '').replaceAll('.git', '')
    PROJECTKEY=reponame
    PROJECTNAME=reponame
    def org = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/', '').replaceAll('.git', '')
    ORG_NAME=org
    }

    githubNotify account: 'kiransre', context: 'QualityGate', credentialsId: 'Github', description: 'Qualitygate is failed', gitApiUrl: '', repo: "$ORG_NAME", sha: "${env.GIT_COMMIT}", status: 'PENDING', targetUrl: ''
    }
  }
}
  }
}    

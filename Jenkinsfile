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
       expression { env.GIT_BRANCH != null } 
    beforeAgent true

        }
   environment {
        scannerHome = tool name: 'RSSONAR', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }
    steps {
      //checkout([$class: 'GitSCM', branches: [[name: '${GITHUB_BRANCH_HEAD_SHA}']], browser: [$class: 'GithubWeb', repoUrl: 'https://github.com/risksense/pr-build-testing/'], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'Github', name: 'origin', url: 'https://github.com/risksense/pr-build-testing.git']]])
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
    expression { env.GIT_BRANCH == null } 
    beforeAgent true
        }
   environment {
        scannerHome = tool name: 'RSSONAR', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }
    steps {
      //checkout([$class: 'GitSCM', branches: [[name: '${GITHUB_PR_HEAD_SHA}']], browser: [$class: 'GithubWeb', repoUrl: 'https://github.com/risksense/pr-build-testing/'], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'Github', name: 'origin', url: 'https://github.com/risksense/pr-build-testing.git']]])
      setGitHubPullRequestStatus context: 'QualityGate', message: 'Scanner is started', state: 'PENDING'
        script {
            def reponame = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('git://github.com/risksense/', '').replaceAll('.git', '')
            PROJECTKEY=reponame
            PROJECTNAME=reponame
            def org = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/', '').replaceAll('.git', '')
            ORG_NAME=org
        }        
        sh "printenv | sort"
        sh "echo ${env.GIT_URL}"
        sh "echo ${env.GIT_BRANCH}"
        sh 'echo "PR sonar build"'
      
        gitHubPRStatus githubPRMessage('${GITHUB_PR_COND_REF} run started')
    }
    post {
    always {
    githubPRComment comment: githubPRMessage('Build ${WORKSPACE}'), errorHandler: statusOnPublisherError('UNSTABLE'), statusVerifier: allowRunOnStatus('SUCCESS')
    }
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

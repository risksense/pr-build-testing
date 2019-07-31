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
        withSonarQubeEnv('RSSONAR') {
            // sh "printenv | sort"
            sh """cd $WORKSPACE/spark
            ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.host.url=http://jenkins.risksense.com:9000 \
                    -Dsonar.login=7d1d5ee36bcfcf38b8f8ede697ee150479ce5771 \
                    -Dsonar.projectKey=$GE_PROJECTKEY \
                    -Dsonar.projectName=$GE_PROJECTNAME \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.branch=$GE_BRANCH_NAME \
                    -Dsonar.scm.provider=git \
                    -Dsonar.sources=. \
                    -Dsonar.java.binaries=**/target \
                    -Dsonar.qualitygate="RiskSense-Legacy" \
                    -Dsonar.java.libraries=$HOME/.m2/**/*.jar
            """
        }        
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
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
        withSonarQubeEnv('RSSONAR') {
            sh """
            mvn clean install
            """
            sh "printenv | sort"
            sh """
            ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.sourceEncoding=UTF-8 \
                    -Dsonar.host.url=http://jenkins.risksense.com:9000 \
                    -Dsonar.login=7d1d5ee36bcfcf38b8f8ede697ee150479ce5771 \
                    -Dsonar.projectKey=$PROJECTKEY \
                    -Dsonar.projectName=$PROJECTNAME \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.branch=${ghprbTargetBranch} \
                    -Dsonar.sources=. \
                    -Dsonar.java.binaries=**/target \
                    -Dsonar.java.libraries=$HOME/.m2/**/*.jar \
                    -Dsonar.analysis.mode=preview \
                    -Dsonar.issuesReport.console.enable=true \
                    -Dsonar.forceUpdate=true \
                    -Dsonar.github.pullRequest=${env.CHANGE_ID} \
                    -Dsonar.github.repository=$ORG_NAME \
                    -Dsonar.github.login=kiransre \
                    -Dsonar.github.oauth=9718ad60-db48-44c9-9347-94a9d4aeee39 \
                    -Dhudson.model.ParametersAction.keepUndefinedParameters=true
            """
        }
    }
    post {
    script {
    def reponame = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/risksense/', '').replaceAll('.git', '')
    PROJECTKEY=reponame
    PROJECTNAME=reponame
    def org = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/', '').replaceAll('.git', '')
    ORG_NAME=org
    }
    success {
      githubNotify account: 'kiransre', context: 'QualityGate', credentialsId: 'Github', description: 'Qualitygate is finished', gitApiUrl: '', repo: "$ORG_NAME", sha: "${env.GIT_COMMIT}", status: 'SUCCESS', targetUrl: ''
    }
    failure {
    githubNotify account: 'kiransre', context: 'QualityGate', credentialsId: 'Github', description: 'Qualitygate is failed', gitApiUrl: '', repo: "$ORG_NAME", sha: "${env.GIT_COMMIT}", status: 'PENDING', targetUrl: ''
    }
  }
}
  }
}    
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
    script {
    def reponame = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/risksense/', '').replaceAll('.git', '')
    PROJECTKEY=reponame
    PROJECTNAME=reponame
    def org = sh(returnStdout: true, script: 'echo /$/"/{env.GIT_URL/}/"').trim().replaceAll('https://github.com/', '').replaceAll('.git', '')
    ORG_NAME=org
    }
    success {
      githubNotify account: 'kiransre', context: 'QualityGate', credentialsId: 'Github', description: 'Qualitygate is finished', gitApiUrl: '', repo: "$ORG_NAME", sha: "${env.GIT_COMMIT}", status: 'SUCCESS', targetUrl: ''
    }
    failure {
    githubNotify account: 'kiransre', context: 'QualityGate', credentialsId: 'Github', description: 'Qualitygate is failed', gitApiUrl: '', repo: "$ORG_NAME", sha: "${env.GIT_COMMIT}", status: 'PENDING', targetUrl: ''
    }
  }
}
  }
}

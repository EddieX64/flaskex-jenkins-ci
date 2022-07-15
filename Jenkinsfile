pipeline {
  agent {
     node {
        label 'ec2-ci'
        }
  }
  options { timestamps () }
  
  stages {
    stage('SCM Checkout') {
      steps {
        cleanWs()
        git branch: '**', changelog: true, poll: false, url: 'https://github.com/EddieX64/flaskex.git' 
      }
    }

    stage('Testing') {
      steps {
        sh '''python3 -m pip install -r requirements.txt
              pylint --fail-under 7 ./*.py
              pytest tests/test_capitalize.py'''
        stash 'workspace'
      }
    }

    stage('Build artifact') {
      environment {
            // get git commit and branch from Jenkins
            GIT_FULL_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
            GIT_COMMIT = GIT_FULL_COMMIT.substring(0,10)
            FULL_PATH_BRANCH = "${sh(script:'git name-rev --name-only HEAD', returnStdout: true)}"
            GIT_BRANCH = FULL_PATH_BRANCH.substring(FULL_PATH_BRANCH.lastIndexOf('/') + 1, FULL_PATH_BRANCH.length()).trim()
           }
      steps {
          node('ec2-ci-ami') {
          cleanWs()
          unstash 'workspace'
          sh '''echo "$GIT_BRANCH""/""$GIT_COMMIT"
          ls -la'''
        }
      }
    }
  }
}
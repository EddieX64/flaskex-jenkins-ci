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
        sh '''sudo pip3 install -r requirements.txt
              pylint --fail-under 6 *.py **/*.py
              pytest tests/*.py'''
        //stash 'workspace'
      }
    }

    stage('Build artifact') {
      environment {
            // get git commit and branch from Jenkins
            GIT_FULL_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
            GIT_COMMIT = GIT_FULL_COMMIT.substring(0,10)
            FULL_PATH_BRANCH = "${sh(script:'git name-rev --name-only HEAD', returnStdout: true)}"
            GIT_BRANCH = FULL_PATH_BRANCH.substring(FULL_PATH_BRANCH.lastIndexOf('/') + 1, FULL_PATH_BRANCH.length()).trim()
            REGION = sh(returnStdout: true, script: 'ec2-metadata -z | rev | cut -c 2- | rev | cut -d " " -f 2').trim()
            INSTANCE_ID = sh(returnStdout: true, script: 'ec2-metadata -i | cut -d " " -f 2').trim()
            AMI_NAME = "${GIT_BRANCH}" + "/" + "${GIT_COMMIT}"
           }
      steps {
          //node('ec2-ci-ami') {
          //cleanWs()
          //unstash 'workspace'
          sh '''
          #sudo pip3 install -r requirements.txt

          if sudo systemctl is-active --quiet flaskex; then
          sudo systemctl stop flaskex
          rm -rf /opt/flaskex/*
          cp -r ./* /opt/flaskex/
          sudo systemctl start flaskex
          else
          rm -rf /opt/flaskex/*
          cp -r ./* /opt/flaskex/
          sudo systemctl enable flaskex
          sudo systemctl start flaskex
          fi
          
          if sudo systemctl is-active --quiet flaskex; then
          aws ec2 create-image \
          --region $REGION \
          --instance-id $INSTANCE_ID \
          --name "$AMI_NAME"
          else
          sudo systemctl status flaskex
          fi'''
        //}
      }
    }
  }
}
pipeline {
  agent {
     node { 
        label 'ec2-ci-asg'
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

    stage('Shell script') {
      steps {
        sh '''pylint $(git ls-files \'*.py\') --exit-zero
              pytest /tests/test_capitalize.py'''
      }
    }
  }
}

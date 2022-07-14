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
              pytest tests/test_capitalize.py'''
      }
    }

    stage('Build AMI') {
            steps {
                script {
                    def proceed = true
                    try {
                        timeout(time: 300, unit: 'SECONDS') {
                            input(message: 'Do you want to build AMI?')
                        }
                    } catch (err) {
                        proceed = false
                        echo "Build aborted, AMI will not be built"
                    }
                    if(proceed) {
                        echo "Will proceed to build AMI"
                        // AMI building steps
                    }
              }
          }
       }
  }
}

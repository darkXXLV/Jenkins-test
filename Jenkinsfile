pipeline {
     agent any
     stages {
         stage('Input') {
            steps {
                input('Do you want to proceed?')
                    }
                }
               
         stage('Upload to AWS') {
              steps {
                  withAWS(region:'eu-west-1',credentials:'aws_creds') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'first-stack.yaml', bucket:'put-here')
                  }
              }
         } 
     }


     post {

        success {
            echo 'I succeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        aborted {
            sh '''
            git branch -D reverted-main
            git branch -D reverted-main
            git checkout -b reverted-main
            git revert HEAD~1
            git push origin reverted-main
            
            '''
        }
    }
}

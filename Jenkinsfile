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
        aborted {
            withCredentials([gitUsernamePassword(credentialsId: 'github_creds', gitToolName: 'Default')]){
               sh '''
                git branch -D reverted-main
                git checkout main
                git pull origin main
                git checkout -D reverted-main
                git revert -m 1 HEAD
                git checkout main
                git merge reverted-main
                git push origin main
                ''' 
            }
 
            
        }
    }
}

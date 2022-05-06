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
                  withCredentials([gitUsernamePassword(credentialsId: 'github_creds', gitToolName: 'Default')]){
                      sh'''
                      git pull origin main
                      '''
                  }
                  withAWS(region:'eu-west-1',credentials:'aws_creds') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'first-stack.yaml', bucket:'put-here')
                  
                  }
                  
              }
         }
         stage('Update stack'){
             steps{
                 withAWS(region:'eu-west-1',credentials:'aws_creds') {
                    cfnUpdate(stack: 'Jenkins', url: 'https://put-here.s3.eu-west-1.amazonaws.com/first-stack.yaml')
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
                git config --global user.email "kristaps8894@gmail.com"
                git config --global user.name "darkXXLV"
                git checkout main
                git pull origin main
                git checkout -b reverted-main
                git revert -m 1 HEAD
                git checkout main
                git merge reverted-main
                git push origin main
                git branch -D reverted-main
                ''' 
            }
 
            
        }
    }
}

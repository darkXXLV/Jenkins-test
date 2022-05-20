pipeline {
     agent any
     stages {
        //  Asks for user input  "Proceed" or "Abort"
         stage('Input') {
            steps {
                input('Do you want to proceed?')
                    }
                }
        // If pressed "proceed", this continues on from here.
        // Uploads to AWS S3
         stage('Upload to AWS') {
              steps {
                  // Pulls newest file from github
                  withCredentials([gitUsernamePassword(credentialsId: 'github_creds', gitToolName: 'Default')]){
                      sh'''
                      git pull origin main
                      '''
                  }
                  // Using plugin, uploads file to AWS S3
                  withAWS(region:'eu-west-1',credentials:'aws_creds') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'first-stack.yaml', bucket:'put-here')
                  
                  }
                  
              }
         }
         // Updates stack using jenkins plugin
         stage('Update stack'){
             steps{
                 withAWS(region:'eu-west-1',credentials:'aws_creds') {
                    cfnUpdate(stack: 'Jenkins', url: 'https://put-here.s3.eu-west-1.amazonaws.com/first-stack.yaml')
                 }
             }
         }
     }


     post {
        // This echos only when the job ahs been successful
        success {
            echo 'I succeeded!'
        }
        //This happens when the job is aborted or in the user input has been pressed "Abort"
        aborted {
            withCredentials([gitUsernamePassword(credentialsId: 'github_creds', gitToolName: 'Default')]){
               sh '''
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

pipeline {
     agent any
     stages {
         stage('Build') {
             steps {
                 sh 'echo "Building..."'
             }
         }      
         stage('Upload to AWS') {
              steps {
                  script {
             def USER_INPUT = input(
                    message: 'Upload to S3 Bucket?',
                    parameters: [
                            [$class: 'ChoiceParameterDefinition',
                             choices: ['no','yes'].join('\n'),
                             name: 'input',
                             description: 'Menu - select box option']
                    ])

            echo "The answer is: ${USER_INPUT}"

            if( "${USER_INPUT}" == "yes"){
                withAWS(region:'eu-west-1',credentials:'jenkins-cred-aws') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'first-stack.yaml', bucket:'matyss-jenkins-bucket')
                      sh'''
                      aws cloudformation update-stack --stack-name matyss-first-stack --template-url https://matyss-jenkins-bucket.s3.eu-west-1.amazonaws.com/first-stack.yaml
                      '''
                  }
            } else {
                withCredentials([gitUsernamePassword(credentialsId: '4985803d-5b3f-42f4-8eef-b4d722633aae', gitToolName: 'Default')]) {
                sh '''
                git checkout main
                git pull origin main
                git checkout -b deletetest
                git revert -m 1 HEAD  
                git checkout main
                git merge deletetest
                git push origin main
                git branch -D deletetest
                '''
            }
            }
        }
    }
    }
}
}
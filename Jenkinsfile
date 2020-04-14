pipeline {
    agent any
    stages {
       stage('Upload to AWS') {
             steps {
                 withAWS(region:'us-east-2', credentials:'aws-static') {
                 sh 'echo "Uploading content with AWS creds"'
                     s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'static-jenkins-pipeline-neha')
                 }
             }
        }
        stage('AWS Credentials') {
            steps {
             withCredentials([[ $class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'blueocean', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                sh """
                        mkdir -p ~/.aws
                        echo "[default]" >~/.aws/credentials
                        echo "[default]" >~/.boto
                        echo "aws_access_key_id = ${AWS_ACCESS_KEY_ID}" >>~/.boto
                        echo "aws_secret_access_key = ${AWS_SECRET_ACCESS_KEY}" >>~/.boto
                        echo "aws_access_key_id = ${AWS_ACCESS_KEY_ID}" >>~/.aws/credentials
                        echo "aws_secret_access_key = ${AWS_SECRET_ACCESS_KEY}" >>~/.aws/credentials          
                """
            }   
            }
        }
        stage('Create EC2 Instance') {
            steps {
                ansiblePlaybook playbook: 'main.yml', inventory: 'inventory'
            }   
        }
    }
}

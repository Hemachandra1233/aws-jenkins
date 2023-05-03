pipeline {

  agent {
    kubernetes {
      yamlFile 'agent.yaml'
    }
  }
  environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION = 'ap-south-1'
        LAMBDA_FUNCTION_NAME = 'jenkinsLambdaDeployment'
    }

  stages {
  
    stage('Run npm') {
      steps {
        container('npm') {
          sh 'npm install'
        }
      }
    }

    stage('Package Application') {
            agent any
            steps {
                sh 'zip -r app.zip *'
            }
        }

    stage('Deploy to AWS Lambda') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "aws lambda update-function-code --function-name ${env.LAMBDA_FUNCTION_NAME} --zip-file fileb://app.zip"
                }
            }
        }
  
  }
}

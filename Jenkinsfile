pipeline {

  agent {
    kubernetes {
      yamlFile 'agent.yaml'
    }
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
                    awsAccessKeyId(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                    awsSecretAccessKey(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh "aws lambda update-function-code --function-name ${env.LAMBDA_FUNCTION_NAME} --zip-file fileb://app.zip"
                }
            }
        }
  
  }
}

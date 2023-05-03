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

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=hemachandra1019/jenkins-nodejs:${BUILD_NUMBER}
            '''
          }
        }
      }
    }

    stage('Deploy App to Kubernetes') {     
      steps {
        container('kubectl') {
          withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
            sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" deploy-svc.yaml'
            sh 'kubectl apply -f deploy-svc.yaml'
          }
        }
      }
    }
  
  }
}

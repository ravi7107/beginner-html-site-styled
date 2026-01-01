pipeline {
  agent any

  triggers {
    githubPush()
  }

  environment {
    IMAGE = "ravi7107/html-site:latest"
  }

  stages {

    stage('Checkout') {
      steps {
        git 'https://github.com/ravi7107/beginner-html-site-styled.git'
      }
    }

    stage('Build Image') {
      steps {
        sh '''
        eval $(minikube docker-env)
        docker build -t $IMAGE .
        '''
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([
          usernamePassword(
            credentialsId: 'docker-creds',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
          )
        ]) {
          sh '''
          echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
          docker push $IMAGE
          '''
        }
      }
    }

    stage('Deploy to Minikube') {
      steps {
        sh '''
        kubectl apply -f deployment.yaml
        kubectl apply -f service.yaml
        '''
      }
    }
  }
}

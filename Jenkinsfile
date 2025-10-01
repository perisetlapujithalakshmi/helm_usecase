pipeline {
    agent any

    environment {
        KUBECONFIG = '/home/ubuntu/.kube/config'  // path to kubeconfig on Jenkins agent
        DOCKER_USERNAME = credentials('docker-creds-username') // DockerHub username
        DOCKER_PASSWORD = credentials('docker-creds-password') // DockerHub token
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/perisetlapujithalakshmi/helm_usecase.git'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sh '''
                  echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                  docker build -t $DOCKER_USERNAME/helloworld:latest .
                  docker push $DOCKER_USERNAME/helloworld:latest
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                  cd helm/helloworld
                  helm upgrade --install helloworld . \
                    --set image.repository=$DOCKER_USERNAME/helloworld \
                    --set image.tag=latest
                '''
            }
        }
    }
}

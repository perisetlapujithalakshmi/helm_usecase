pipeline {
    agent any

    environment {
        KUBECONFIG = '/home/ubuntu/.kube/config'  // path to kubeconfig on Jenkins agent

    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/perisetlapujithalakshmi/helm_usecase.git'
            }
        }

        stage("Build Docker Image") {
            steps {
                sh "docker build -t $DOCKERHUB_USER/$IMAGE_NAME:$IMAGE_TAG ."
            }
        }

        stage("Push the Image to DockerHub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKERHUB_USER/$IMAGE_NAME:$IMAGE_TAG'
                }
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

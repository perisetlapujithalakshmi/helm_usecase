pipeline {
    agent any

    environment {
        KUBECONFIG = '/home/ubuntu/.kube/config'
        IMAGE_NAME = "helloworld"
        IMAGE_TAG  = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/perisetlapujithalakshmi/helm_usecase.git'
            }
        }

        stage("Build Docker Image") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh "docker build -t $DOCKERHUB_USER/$IMAGE_NAME:$IMAGE_TAG ."
                }
            }
        }

        stage("Push the Image to DockerHub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
                    sh "docker push $DOCKERHUB_USER/$IMAGE_NAME:$IMAGE_TAG"
                }
            }
        }
        stage('Print Workspace') {
    steps {
        sh 'echo $WORKSPACE'
    }
}

        stage('Deploy to Kubernetes') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
            sh """
                cd ${WORKSPACE}/helm/helloworld
                helm upgrade --install helloworld . \
                    --set image.repository=pujithaperisetla01/helloworld \
                    --set image.tag=latest
            """
        }
    }
}

    }
}

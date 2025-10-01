pipeline {
    agent any

    environment {
        KUBECONFIG = '/home/ubuntu/.kube/config'
        IMAGE_NAME = "hello"
        IMAGE_TAG  = "latest"
    }

    stages {

        stage('Clone Repository') {
            steps {
                // Clean workspace and clone fresh repo
                sh "rm -rf ${WORKSPACE}/*"
                sh "git clone -b main https://github.com/perisetlapujithalakshmi/helm_usecase.git ${WORKSPACE}/helm_repo"
            }
        }

        stage("Build Docker Image") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh """
                        cd ${WORKSPACE}/helm_repo/helm
                        docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} .
                    """
                }
            }
        }

        stage("Push the Image to DockerHub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh """
                        echo ${DOCKERHUB_PASS} | docker login -u ${DOCKERHUB_USER} --password-stdin
                        docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    cd ${WORKSPACE}/helm_repo/helm/helloworld
                    helm install helloworld-$(date +%s) . \
                        --set image.repository=${DOCKERHUB_USER}/${IMAGE_NAME} \
                        --set image.tag=${IMAGE_TAG}
                """
            }
        }
    }
}

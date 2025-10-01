pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'pujithaperisetla01'
        IMAGE_NAME     = 'helloworld'
        IMAGE_TAG      = 'latest'
        KUBECONFIG     = '/home/ubuntu/.kube/config'
    }

    stages {
        stage("Clone Helm Repo") {
            steps {
                dir("${WORKSPACE}/helm_usecase") {
                    git branch: 'main', url: 'https://github.com/perisetlapujithalakshmi/helm_usecase.git'
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                dir("${WORKSPACE}/helm_usecase") {
                    sh "docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage("Push Docker Image") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER_VAR', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh "echo ${DOCKERHUB_PASS} | docker login -u ${DOCKERHUB_USER_VAR} --password-stdin"
                    sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage("Deploy Helm Chart") {
            steps {
                dir("${WORKSPACE}/helm_usecase/helloworld") {
                    sh """
                        helm upgrade --install helloworld . \
                            --set image.repository=${DOCKERHUB_USER}/${IMAGE_NAME} \
                            --set image.tag=${IMAGE_TAG}
                    """
                }
            }
        }
    }
}

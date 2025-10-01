pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'dockerhub-username/hello'
        DOCKER_TAG = 'latest'
        HELM_RELEASE_NAME = 'helloworld-release'
        HELM_CHART_PATH = './helloworld'
        K8S_NAMESPACE = 'default'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-credentials'
    }

    stages {
        stage('Checkout Code') {
    steps {
        git branch: 'main', url: 'https://github.com/perisetlapujithalakshmi/helm_usecase.git'
    }
}


        stage('Helm Deploy') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    script {
                        sh """
                            echo "$PASS" | docker login -u "$USER" --password-stdin
                            helm upgrade --install ${HELM_RELEASE_NAME} ${HELM_CHART_PATH} \
                                --set image.repository=${DOCKER_IMAGE} \
                                --set image.tag=${DOCKER_TAG} \
                                --namespace ${K8S_NAMESPACE}
                        """
                    }
                }
            }
        }
    }
}

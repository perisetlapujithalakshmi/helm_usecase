pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'pujithaperisetla01/hello'
        DOCKER_TAG = 'latest'
        HELM_RELEASE_NAME = 'helloworld-release'
        HELM_CHART_PATH = './helloworld'
        K8S_NAMESPACE = 'default'
        KUBECONFIG_PATH = '/data/kube/config'
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
                    export KUBECONFIG=/data/kube/config
                    helm uninstall hello --namespace default || true
                    helm install hello ./helloworld \
                        --set image.repository=pujithaperisetla01/hello \
                        --set image.tag=latest \
                        --namespace default
                """
            }
        }
    }
}

        }
    }

pipeline {
    agent any

    environment {
        IMAGE = 'hello-world'
        TAG = "${env.BUILD_NUMBER}"
        KUBECONFIG = credentials('kubeconfig-credential-id')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'eval $(minikube -p minikube docker-env)'
                    sh 'docker build -t ${IMAGE}:${TAG} .'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig-credential-id', variable: 'KUBECONFIG_FILE')]) {
                        sh 'mkdir -p $HOME/.kube'
                        sh 'cp $KUBECONFIG_FILE $HOME/.kube/config'
                        sh 'kubectl config use-context minikube'
                        sh 'kubectl apply -f k8s/deployment.yaml --validate=false'
                        sh 'kubectl apply -f k8s/service.yaml --validate=false'
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

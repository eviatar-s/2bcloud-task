pipeline {
    agent any

    environment {
        IMAGE = 'hello-world-app'
        TAG = "${env.BUILD_NUMBER}"
        KUBECONFIG = credentials('kubeconfig-credential-id')
    }

    stages {
        stage('Setup Minikube') {
            steps {
                script {
                    sh 'minikube start --driver=docker'
                }
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker Image..."
                    sh 'eval $(minikube -p minikube docker-env)'
                    sh 'docker build -t ${IMAGE}:${TAG} .'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying to Kubernetes..."
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
            echo "Cleaning workspace..."
            cleanWs()
        }
    }
}

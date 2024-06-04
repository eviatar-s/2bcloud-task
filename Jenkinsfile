pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Define Dockerfile location
                    def dockerfile = 'Dockerfile'
                    
                    // Build Docker image
                    docker.build('hello-world:latest', '-f ${dockerfile} .')
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy application to Kubernetes using kubectl
                    sh 'kubectl apply -f path/to/kubernetes-manifests'
                }
            }
        }
    }
}


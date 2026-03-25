pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Vaishnavibitla4/node-k8s-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                docker build -t my-k8s-app:%BUILD_NUMBER% .
                docker tag my-k8s-app:%BUILD_NUMBER% vaishnavibitla/my-k8s-app:v1
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push vaishnavibitla/my-k8s-app:v1'
            }
        }

        stage('Start Minikube if not running') {
            steps {
                bat '''
                if ! minikube status | grep -q "apiserver: Running"; then
                    echo "Minikube is not running. Starting now..."
                    minikube start --driver=docker --memory=2048 --cpus=2
                fi
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                # Load latest image into Minikube
                # minikube image load laxmi916/my-k8s-app:v1

                # Apply manifests
                minikube kubectl -- apply -f k8s/deployment.yaml
                minikube kubectl -- apply -f k8s/service.yaml
                minikube service my-k8s-app-service
                '''
            }
        }
    }
}

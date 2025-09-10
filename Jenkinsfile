pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-jenkins-demo"
    }

    triggers {
        pollSCM('H * * * *')  // check Git every hour
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Pushpa-devops/Devops-learn-Kutty.git'
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                sh '''
                echo "➡️ Building Docker image inside Minikube..."
                minikube image build -t ${IMAGE_NAME}:latest .
                minikube image ls | grep ${IMAGE_NAME} || true
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                echo "➡️ Applying Deployment and Service..."
                minikube kubectl -- apply -f k8s-deployment.yaml
                minikube kubectl -- apply -f k8s-service.yaml

                echo "➡️ Waiting for rollout..."
                minikube kubectl -- rollout status deployment/nginx-demo
                '''
            }
        }

        stage('Access URL') {
            steps {
                sh '''
                echo "➡️ Pods:"
                minikube kubectl -- get pods -o wide

                echo "➡️ Service URL:"
                minikube service nginx-demo-service --url
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ Pipeline failed. Check console logs."
        }
        success {
            echo "✅ Deployment successful!"
        }
    }
}

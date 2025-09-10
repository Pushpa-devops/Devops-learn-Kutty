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

                echo "✅ Image built successfully:"
                docker images | grep ${IMAGE_NAME} || true
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                echo "➡️ Applying Deployment via Minikube CLI..."
                minikube kubectl -- apply -f k8s-deployment.yaml
                minikube kubectl -- apply -f k8s-service.yaml

                echo "➡️ Waiting for deployment rollout..."
                minikube kubectl -- rollout status deployment/nginx-test
                '''
            }
        }

        stage('Verify & Print URL') {
            steps {
                sh '''
                echo "➡️ Pods:"
                minikube kubectl -- get pods -o wide

                echo "➡️ Services:"
                minikube kubectl -- get svc nginx-test-service

                echo "➡️ Access Nginx using this URL:"
                minikube service nginx-test-service --url
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ Pipeline failed. Check console output."
        }
        success {
            echo "✅ Deployment successful! 🎉"
        }
    }
}

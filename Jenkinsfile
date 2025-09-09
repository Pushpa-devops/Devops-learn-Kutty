pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-jenkins-minikube"
    }

    triggers {
        pollSCM('H * * * *')  // check every 1 hr
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'k8s-Project', url: 'https://github.com/Pushpa-devops/Jenkins-Project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                    sh '''
                    #!/bin/bash
                    docker build -t nginx-jenkins-minikube:latest .
                    '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh """
                kubectl apply -f k8s-deployment.yaml
                kubectl apply -f k8s-service.yaml
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Access app at: minikube service nginx-demo-service --url"
        }
        failure {
            echo "❌ Build or deploy failed."
        }
    }
}


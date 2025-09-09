pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-jenkins-minikube"
        KUBECONFIG_PATH = "/var/lib/jenkins/.kube/config"  // path to Jenkins user's kubeconfig
    }

    triggers {
        pollSCM('H * * * *')  // check for new commits once every hour
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'k8s-Project', url: 'https://github.com/Pushpa-devops/Jenkins-Project.git'
            }
        }

        stage('Set up Minikube Docker Environment') {
            steps {
                // Make sure Docker points to Minikube daemon
                sh '''
                #!/bin/bash
                if ! minikube status >/dev/null 2>&1; then
                    echo "Starting Minikube..."
                    minikube start --driver=docker
                fi

                echo "Configuring Docker to use Minikube's Docker daemon..."
                eval $(minikube docker-env)
                docker version
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                #!/bin/bash
                echo "Building Docker image for Minikube..."
                docker build -t ${IMAGE_NAME}:latest .
                docker images | grep ${IMAGE_NAME}
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                #!/bin/bash
                export KUBECONFIG=${KUBECONFIG_PATH}
                echo "Applying Kubernetes deployment..."
                kubectl apply -f k8s-deployment.yaml
                kubectl apply -f k8s-service.yaml
                kubectl get pods
                kubectl get svc
                '''
            }
        }

    }

    post {
        success {
            echo "✅ Deployment successful! You can access the app using:"
            echo "minikube service nginx-demo-service --url"
        }
        failure {
            echo "❌ Pipeline failed. Check console logs for details."
        }
    }
}

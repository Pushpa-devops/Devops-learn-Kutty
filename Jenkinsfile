pipeline {
    agent any

    environment {
        KUBECONFIG_PATH = "/home/jenkins/.kube/config"
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
                echo "➡️ Switching Docker to Minikube..."
                eval $(minikube docker-env)

                echo "➡️ Building Docker image..."
                docker build -t nginx-jenkins-demo:latest .

                echo "✅ Image built successfully:"
                docker images | grep nginx-jenkins-demo
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                export KUBECONFIG=${KUBECONFIG_PATH}

                echo "➡️ Applying Deployment..."
                sed -i 's|image: nginx:alpine|image: nginx-jenkins-demo:latest\\n        imagePullPolicy: Never|' k8s-deployment.yaml
                kubectl apply -f k8s-deployment.yaml

                echo "➡️ Applying Service..."
                kubectl apply -f k8s-service.yaml

                echo "➡️ Waiting for rollout..."
                kubectl rollout status deployment/nginx-test
                '''
            }
        }

        stage('Verify & Print URL') {
            steps {
                sh '''
                export KUBECONFIG=${KUBECONFIG_PATH}

                echo "➡️ Pods:"
                kubectl get pods -o wide

                echo "➡️ Services:"
                kubectl get svc nginx-test-service

                echo "➡️ Access Nginx using:"
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

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

        stage('Build Docker Image & Deploy') {
    steps {
        sh '''
        echo "➡️ Configuring Docker for Minikube..."
        eval $(minikube docker-env)

        echo "➡️ Building Docker image..."
        docker build -t nginx-jenkins-demo:latest .

        echo "➡️ Deploying manifests..."
        kubectl apply -f k8s/deployment.yaml --validate=false
        kubectl apply -f k8s/service.yaml --validate=false

        echo "➡️ Rollout status..."
        kubectl rollout status deployment/nginx-test
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

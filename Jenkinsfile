pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-jenkins-demo"
        CONTAINER_NAME = "nginx-demo-container"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Pushpa-devops/Jenkins-Project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  echo "Building Docker image..."
                  docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                  echo "Stopping old container if exists..."
                  docker stop $CONTAINER_NAME || true
                  docker rm $CONTAINER_NAME || true

                  echo "Starting new container..."
                  docker run -d --name $CONTAINER_NAME -p 9080:80 $IMAGE_NAME
                '''
            }
        }
    }
}


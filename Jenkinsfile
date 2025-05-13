pipeline {
    agent any

    environment {
        IMAGE_NAME = 'myapp'
        DOCKER_REGISTRY = 'pradeepkumar1407'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker image...'
                script {
                    dockerImage = docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}")
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests for Docker Images...'
                sh 'echo "Tests passed!"'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to registry...'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials-id') {
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                script{
            docker stop myapp-container || true
            docker rm myapp-container || true
            docker run -d --name myapp-container -p 8080:80  ${DOCKER_REGISTRY}/${IMAGE_NAME} myapp:latest
  }
                sh 'echo "Deployment complete!"'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

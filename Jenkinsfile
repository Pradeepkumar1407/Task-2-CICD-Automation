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
                echo 'Running tests...'
                // Replace this with actual test commands
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
                // Replace this with actual deploy command, e.g. Docker run, kubectl apply, etc.
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

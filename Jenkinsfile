pipeline {
    agent any

    environment {
        // Name of your Docker image (adjust if needed)
        DOCKER_IMAGE = "pradeepkumar1407/my-node-app"
        
        // EC2 deployment configuration
        EC2_USER = "ec2-user"
        EC2_HOST = "your-ec2-instance-ip-or-dns" // update with your EC2 instance public IP/DNS
        
        // Jenkins credentials IDs configured in Jenkins
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-creds')
        SSH_KEY = credentials('ec2-ssh-key')
    }

    stages {
        stage('Checkout') {
            steps {
                // Check out the code from your SCM
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    // Run tests (currently a placeholder)
                    sh "npm test"
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub
                    sh """
                    echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin
                    """
                    // Push the built image to Docker Hub
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                script {
                    // SSH into your EC2 instance and deploy the container.
                    // This command pulls the latest image, stops and removes any previous container,
                    // and runs the new container mapping container's port 3000 to host port 3000.
                    sh """
                    ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ${EC2_USER}@${EC2_HOST} '
                        docker pull ${DOCKER_IMAGE} &&
                        docker stop my-node-app || true &&
                        docker rm my-node-app || true &&
                        docker run -d --name my-node-app -p 3000:3000 ${DOCKER_IMAGE}
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

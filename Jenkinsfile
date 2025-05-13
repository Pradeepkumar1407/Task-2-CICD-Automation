pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "pradeepkumar1407/my-node-app"
        EC2_USER = "ubuntu"
        EC2_HOST = "44.204.159.246"
        
        // Jenkins credentials IDs configured in Jenkins
        DOCKER_HUB_CREDENTIALS = credentials('Docker ID')
        SSH_KEY = credentials('7784556d-a0e2-4de7-a68c-ef348074b12c')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    sh 'docker run --rm pradeepkumar1407/my-node-app npm test'
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    sh """
                    echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin
                    """
                    // Push the built image to Docker Hub
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        
       stage('Deploy') {
  steps {
    withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'SSH_KEYFILE')]) {
      sh '''
        ssh -o StrictHostKeyChecking=no -i $SSH_KEYFILE ubuntu@44.204.159.246 '
          docker pull pradeepkumar1407/my-node-app &&
          docker stop my-node-app || true &&
          docker rm my-node-app || true &&
          docker run -d --name my-node-app -p 3000:3000 pradeepkumar1407/my-node-app
        '
      '''
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

pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'hassan9111' 
        IMAGE_NAME = "${DOCKER_HUB_USER}/lab-midterm-fastapi:latest"
        CONTAINER_NAME = 'midterm-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Train Model') {
            steps {
                sh 'python train.py' 
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}"
                }
            }
        }

        stage('Deploy on EC2') {
            steps {
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
                sh "docker run -d -p 8000:8000 --name ${CONTAINER_NAME} ${IMAGE_NAME}"
            }
        }
    }

    post {
        always {
            echo "Pipeline finished for Student FA23-BDS-015"
            // We will add the actual email code later once you configure SMTP
        }
    }
}
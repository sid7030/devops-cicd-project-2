pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'siddharthpingle7030/devops-cicd-project-2'
        EC2_HOST = '44.214.181.34'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                bat '"C:\\Users\\acer\\AppData\\Local\\Programs\\Python\\Python314\\python.exe" -m pytest'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE%:latest .'
            }
        }

        stage('Login Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat 'docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    bat '''
                        ssh -o StrictHostKeyChecking=no ubuntu@%EC2_HOST% "docker pull %DOCKER_IMAGE%:latest && docker stop devops-cicd-project-2 || true && docker rm devops-cicd-project-2 || true && docker run -d -p 5001:5000 --name devops-cicd-project-2 %DOCKER_IMAGE%:latest"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD pipeline failed.'
        }
    }
}
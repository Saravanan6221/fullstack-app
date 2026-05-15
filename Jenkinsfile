pipeline {

    agent none

    environment {

        AWS_REGION = 'ap-south-1'

        ACCOUNT_ID = 'YOUR-AWS-ACCOUNT-ID'

        FRONTEND_IMAGE = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/frontend:latest"

        BACKEND_IMAGE = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/backend:latest"
    }

    stages {

        stage('Clone Repository') {

            agent { label 'linux' }

            steps {
                git 'YOUR-GITHUB-REPO'
            }
        }

        stage('Frontend Build Test') {

            agent { label 'linux' }

            steps {
                dir('frontend') {
                    sh 'ls -la'
                }
            }
        }

        stage('Backend Build Test') {

            agent { label 'linux' }

            steps {
                dir('backend') {
                    sh 'npm install'
                }
            }
        }

        stage('Docker Build Frontend') {

            agent { label 'docker' }

            steps {
                sh 'docker build -t frontend:latest ./frontend'
            }
        }

        stage('Docker Build Backend') {

            agent { label 'docker' }

            steps {
                sh 'docker build -t backend:latest ./backend'
            }
        }

        stage('Docker Tag Images') {

            agent { label 'docker' }

            steps {

                sh "docker tag frontend:latest ${FRONTEND_IMAGE}"

                sh "docker tag backend:latest ${BACKEND_IMAGE}"
            }
        }

        stage('Login AWS ECR') {

            agent { label 'docker' }

            steps {

                sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
            }
        }

        stage('Push Frontend Image') {

            agent { label 'docker' }

            steps {
                sh "docker push ${FRONTEND_IMAGE}"
            }
        }

        stage('Push Backend Image') {

            agent { label 'docker' }

            steps {
                sh "docker push ${BACKEND_IMAGE}"
            }
        }

        stage('Kubernetes Deployment') {

            agent { label 'k8s' }

            steps {

                sh 'kubectl apply -f k8s/frontend-deployment.yaml'

                sh 'kubectl apply -f k8s/frontend-service.yaml'

                sh 'kubectl apply -f k8s/backend-deployment.yaml'

                sh 'kubectl apply -f k8s/backend-service.yaml'
            }
        }
    }
}

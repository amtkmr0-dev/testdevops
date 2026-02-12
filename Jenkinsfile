pipeline {
    agent any

    environment {
        AWS_REGION    = "ap-south-1"
        ECR_REGISTRY  = "688094241179.dkr.ecr.ap-south-1.amazonaws.com/jenkins/repo"
        ECR_REPO      = "my-student-app"
        IMAGE_TAG     = "${BUILD_NUMBER}"
        APP_EC2_HOST  = "65.0.105.213"
        CONTAINER_NAME = "student-app"
        APP_PORT      = "80"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${ECR_REPO}:${IMAGE_TAG} .
                    docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_REPO}:latest
                """
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin ${ECR_REGISTRY}
                """
            }
        }

        stage('Tag & Push to ECR') {
            steps {
                sh """
                    docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}
                    docker tag ${ECR_REPO}:latest ${ECR_REGISTRY}/${ECR_REPO}:latest

                    docker push ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}
                    docker push ${ECR_REGISTRY}/${ECR_REPO}:latest
                """
            }
        }

        stage('Deploy to App EC2') {
            steps {
                sshagent(credentials: ['app-ec2-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@${APP_EC2_HOST} '
                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login --username AWS --password-stdin ${ECR_REGISTRY} &&

                        docker pull ${ECR_REGISTRY}/${ECR_REPO}:latest &&

                        docker rm -f ${CONTAINER_NAME} || true &&

                        docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:80 \
                        ${ECR_REGISTRY}/${ECR_REPO}:latest
                    '
                    """
                }
            }
        }
    }
}

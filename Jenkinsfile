pipeline {
    agent any

    environment {
        AWS_REGION    = "ap-south-1"
        ECR_REGISTRY  = "688094241179.dkr.ecr.ap-south-1.amazonaws.com"
        ECR_REPO      = "jenkins/repo"
        IMAGE_TAG     = "${BUILD_NUMBER}"
        APP_EC2_HOST  = "3.108.217.189"  // ✅ Confirmed
        CONTAINER_NAME = "student-app"
        APP_PORT      = "80"
        ECR_IMAGE     = "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}"
        ECR_LATEST    = "${ECR_REGISTRY}/${ECR_REPO}:latest"
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
                    aws ecr get-login-password --region ${AWS_REGION} | \\
                    docker login --username AWS --password-stdin ${ECR_REGISTRY}
                """
            }
        }

        stage('Tag & Push to ECR') {
            steps {
                sh """
                    docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_IMAGE}
                    docker tag ${ECR_REPO}:latest ${ECR_LATEST}
                    docker push ${ECR_IMAGE}
                    docker push ${ECR_LATEST}
                """
            }
        }

        stage('Deploy to App EC2') {
            steps {
                sshagent(credentials: ['app-ec2-ssh']) {  // ✅ Matches your config[file:262]
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${APP_EC2_HOST} '
                            aws ecr get-login-password --region ${AWS_REGION} | \\
                            docker login --username AWS --password-stdin ${ECR_REGISTRY} &&
                            
                            docker pull ${ECR_LATEST} &&
                            
                            docker rm -f ${CONTAINER_NAME} || true &&
                            
                            docker run -d \\
                            --name ${CONTAINER_NAME} \\
                            -p ${APP_PORT}:80 \\
                            ${ECR_LATEST}
                        '
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}

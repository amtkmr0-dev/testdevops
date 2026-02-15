pipeline {
    agent any

    environment {
        AWS_REGION   = "ap-south-1"
        ECR_REGISTRY = "688094241179.dkr.ecr.ap-south-1.amazonaws.com"
        ECR_REPO     = "jenkins/repo"
        IMAGE_TAG    = "${BUILD_NUMBER}"
        ECR_IMAGE    = "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}"
        ECR_LATEST   = "${ECR_REGISTRY}/${ECR_REPO}:latest"
        APP_EC2_HOST = "3.108.217.189"
        APP_USER     = "ubuntu"
        CONTAINER    = "jenkinsapp"
        APP_PORT     = "80"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    sh """
                        docker build -t ${ECR_REPO}:${IMAGE_TAG} .
                        docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_REPO}:latest
                    """
                }
            }
        }

        stage('ECR Login & Push') {
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                        docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_IMAGE}
                        docker tag ${ECR_REPO}:latest ${ECR_LATEST}
                        docker push ${ECR_IMAGE}
                        docker push ${ECR_LATEST}
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                retry(3) {
                    sshagent(credentials: ['app-ec2-ssh']) {
                        sh """
                            ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10 ${APP_USER}@${APP_EC2_HOST} '
                                aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY} &&
                                
                                docker pull ${ECR_LATEST} &&
                                
                                docker stop ${CONTAINER} || true
                                docker rm ${CONTAINER} || true &&
                                
                                docker run -d \\
                                    --name ${CONTAINER} \\
                                    --restart unless-stopped \\
                                    -p ${APP_PORT}:80 \\
                                    ${ECR_LATEST} &&
                                
                                docker image prune -f
                            '
                        """
                    }
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

pipeline {
    agent any

    environment {
        AWS_REGION   = "ap-south-1"
        ECR_REGISTRY = "688094241179.dkr.ecr.ap-south-1.amazonaws.com"
        ECR_REPO     = "jenkins/repo"
        IMAGE_TAG    = "${BUILD_NUMBER}"
        ECR_IMAGE    = "${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}"
        ECR_LATEST   = "${ECR_REGISTRY}/${ECR_REPO}:latest"
        APP_EC2_HOST = "52.66.237.173"
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
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ubuntu-ec2',
                            transfers: [
                                sshTransfer(
                                    execCommand: """
                                        docker login -u AWS -p \$(aws ecr get-login-password --region ${AWS_REGION}) ${ECR_REGISTRY}
                                        docker rm -f myapp || true
                                        docker run -d -p ${APP_PORT}:${APP_PORT} --name myapp ${ECR_LATEST}
                                    """
                                )
                            ],
                            verbose: true
                        )
                    ]
                )
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}

# testdevops

A practical DevOps demonstration project showcasing containerization, CI/CD automation, and cloud deployment workflows.

## Problem it solves

This project demonstrates a complete end-to-end DevOps workflow, from containerizing a web application to automating its deployment through Jenkins. It serves as a reference implementation for developers learning DevOps practices and CI/CD pipeline design.

## Features

- **Containerization**: Docker-based packaging of a static web application
- **CI/CD Pipeline**: Automated build, test, and deployment using Jenkins
- **Cloud Registry**: Integration with AWS Elastic Container Registry (ECR)
- **Automated Deployment**: Zero-downtime deployment to EC2 instances via SSH
- **Pipeline Cleanup**: Automated resource cleanup after builds

## Tech stack

- **Container Runtime**: Docker
- **CI/CD Tool**: Jenkins
- **Cloud Provider**: AWS (ECR, EC2)
- **Web Server**: Nginx (Alpine)
- **Version Control**: Git

## Project structure

```
testdevops/
├── Dockerfile          # Container image definition
├── Jenkinsfile         # CI/CD pipeline configuration
└── index.html         # Static web application
```

## Getting started

### Prerequisites

- Docker installed locally
- Jenkins instance with required plugins
- AWS account with ECR and EC2 access
- SSH access to target EC2 instance

### How to run locally

1. **Build the Docker image:**
   ```bash
   docker build -t testdevops:latest .
   ```

2. **Run the container:**
   ```bash
   docker run -d -p 8080:80 --name testdevops testdevops:latest
   ```

3. **Access the application:**
   Open your browser at `http://localhost:8080`

## CI/CD workflow

The Jenkins pipeline automates the following stages:

1. **Checkout Code**: Retrieves the latest code from the repository
2. **Build Docker Image**: Creates a container image with the application
3. **Login to ECR**: Authenticates with AWS Elastic Container Registry
4. **Tag & Push to ECR**: Tags and pushes images to the cloud registry
5. **Deploy to App EC2**: Pulls the latest image and deploys to the target server
6. **Cleanup**: Removes unused Docker resources

### Pipeline stages

| Stage | Description |
|-------|-------------|
| Checkout Code | Clones the repository |
| Build Docker Image | Builds container image with build number tag |
| Login to ECR | Authenticates with AWS ECR |
| Tag & Push to ECR | Pushes tagged images to registry |
| Deploy to App EC2 | Deploys to production server via SSH |

## Use cases

- **Learning DevOps**: Understand CI/CD pipeline concepts and implementation
- **Pipeline Reference**: Use as a template for similar deployment workflows
- **Containerization Demo**: See how to package static web applications
- **AWS Integration**: Learn ECR and EC2 integration with Jenkins

## Roadmap

- [ ] Add automated testing stage to the pipeline
- [ ] Implement rollback mechanism for failed deployments
- [ ] Add health checks and monitoring
- [ ] Support multiple environment deployments (dev, staging, prod)
- [ ] Add Kubernetes deployment option
- [ ] Implement blue-green deployment strategy

## Maintainer

- **GitHub**: [@amtkmr0-dev](https://github.com/amtkmr0-dev)

## License

This project is provided as-is for educational and demonstration purposes.

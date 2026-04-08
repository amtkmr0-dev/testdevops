# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2025-01-XX

### Added
- Initial release of testdevops project
- Dockerfile for containerizing the web application
- Jenkinsfile with complete CI/CD pipeline configuration
- Static web application (index.html) with Jenkins CI/CD presentation
- AWS ECR integration for container registry
- Automated deployment to EC2 via SSH
- Pipeline cleanup stage for resource management

### Features
- Container-based deployment using Docker and Nginx Alpine
- Multi-stage Jenkins pipeline (Checkout, Build, Push, Deploy)
- AWS ECR authentication and image management
- Zero-downtime deployment workflow
- Automated Docker system cleanup

### Documentation
- Comprehensive README with project overview
- Getting started guide for local development
- CI/CD workflow documentation
- Project structure and use cases

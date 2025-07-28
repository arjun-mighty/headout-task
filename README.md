# Headout Task - Automated  Application Deployment

This project implements an automated deployment pipeline for a Java application using GitHub Actions, Docker, and AWS infrastructure.

## 📋 Task Overview

The solution addresses the following requirements:
1. Clone a GitHub repository containing a JAR file via SSH
2. Generate a Dockerfile for containerization
3. Build and deploy the application using GitHub Actions
4. Deploy to AWS EC2 with Load Balancer setup

## 🏗️ Architecture

```
GitHub Repository → GitHub Actions → Docker Image → AWS EC2 → Load Balancer → Live Application
```

## 📁 Project Structure

```
headout-task/
├── .github/workflows/deploy.yml    # GitHub Actions workflow
├── main.py                         # Deployment script
└── README.md                       # This file
```

## 🔧 Components

### 1. GitHub Actions Workflow (`.github/workflows/deploy.yml`)

**Purpose**: Automates the entire deployment process

**Key Steps**:
- Checks out the current repository
- Sets up SSH authentication for private repository access
- Installs Python package manager (uv)
- Runs the deployment script (`main.py`)
- Builds Docker image
- Transfers image to EC2 via SCP
- Starts the container on EC2

**Required Secrets**:
- `GH_SSH_KEY`: SSH private key for GitHub repository access
- `AWS_HOST`: EC2 instance public IP/hostname
- `AWS_SSH_KEY`: SSH private key for EC2 access

### 2. Deployment Script (`main.py`)

**Purpose**: Handles repository cloning and Dockerfile generation

**Functionality**:
- Clones the target repository (`headout_jar`) containing the JAR file
- Automatically detects the most recent JAR file in `build/lib/`
- Generates a Docker configuration for the Java application

**Generated Dockerfile Template**:
```dockerfile
FROM eclipse-temurin:24.0.1_9-jre
COPY build/lib/ /app
WORKDIR /app
ENTRYPOINT ["java", "-jar", "{jarfile}"]
```

## 🚀 Deployment Process

### Automatic Deployment
1. Push code to the `main` branch
2. GitHub Actions triggers automatically
3. Script clones the Java application repository
4. Docker image is built and deployed to EC2
5. Application runs on port 9000 (mapped to port 80)

### Manual Deployment
Use GitHub's "Run workflow" feature in the Actions tab for manual deployments.

## ⚙️ Setup Instructions

### Prerequisites
- AWS EC2 instance running Ubuntu
- SSH access configured for both GitHub and EC2
- Docker installed on EC2 instance
- GitHub repository with Actions enabled

### Configuration Steps

1. **Set up GitHub Secrets**:
   - Go to Repository Settings → Secrets and variables → Actions
   - Add the required secrets mentioned above

2. **EC2 Instance Setup**:
   ```bash
   # Install Docker
   sudo apt update
   sudo apt install docker.io -y
   sudo systemctl start docker
   sudo systemctl enable docker
   sudo usermod -aG docker ubuntu
   ```

3. **Load Balancer Configuration**:
   - Create an Application Load Balancer in AWS
   - Configure target group pointing to EC2 instance port 80
   - Set up health checks for `/` endpoint

## 🔍 Key Features

- **Automated JAR Detection**: Automatically finds the most recent JAR file
- **Zero-Downtime Deployment**: Stops old containers before starting new ones
- **Containerized Deployment**: Uses Docker for consistent environments
- **Infrastructure as Code**: GitHub Actions handles the entire pipeline
- **Load Balancing**: AWS ELB distributes traffic across instances

## 📝 Assumptions & Decisions

### Assumptions Made:
1. **JAR Location**: JAR files are located in `build/lib/` directory
2. **Java Version**: Application runs on Java 24 (Eclipse Temurin JRE)
3. **Port Configuration**: Application starts on port 9000, exposed via port 80
4. **SSH Access**: Both GitHub and EC2 are accessible via SSH keys
5. **Ubuntu Environment**: EC2 instance runs Ubuntu with `ubuntu` user

### Technical Decisions:
1. **Python for Scripting**: Used Python with `loguru` for clear logging
2. **Docker Strategy**: Single-stage build for simplicity
3. **Container Management**: Stop/remove existing containers before deployment
4. **Image Transfer**: Use `docker save/load` instead of registry for simplicity
5. **UV Package Manager**: Modern Python package manager for faster installs

## 🌐 Live Deployment

The application is successfully deployed and accessible via:
- **EC2 Direct Access**: `http://http://54.237.217.175/`
- **Load Balancer**: `http://loadb-1827154874.us-east-1.elb.amazonaws.com`

## 🔧 Troubleshooting

### Common Issues:
1. **SSH Connection Failures**: Verify SSH keys are correctly configured
2. **Docker Permission Errors**: Ensure user is in docker group
3. **Port Conflicts**: Check if port 80 is available on EC2
4. **JAR Not Found**: Verify the repository structure matches expectations

### Debugging:
- Check GitHub Actions logs for detailed error messages
- SSH into EC2 to verify container status: `docker ps`
- Check application logs: `docker logs headout-webserver`

## 🌐 Jar file


- **Jar file Access**: `https://github.com/arjun-mighty/headout_jar`

---

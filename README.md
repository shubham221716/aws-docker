# 🚀 AWS Docker CI/CD Static Website Deployment

This project demonstrates a complete **DevOps pipeline** to deploy a static website using:

* 🐳 Docker (Containerization)
* ☁️ AWS EC2 (Hosting)
* 🔄 GitHub Actions (CI/CD)
* 📦 Docker Hub (Image Registry)
* 🌐 Nginx (Web Server)

---

# 📌 Project Overview

This project automates the deployment of a static website using a modern CI/CD pipeline:

```
GitHub Push
   ↓
GitHub Actions (Build Docker Image)
   ↓
Push to Docker Hub
   ↓
EC2 pulls latest image
   ↓
Runs container (Nginx server)
```

---

# 🧱 Tech Stack

* Docker
* AWS EC2 (Ubuntu)
* GitHub Actions
* Docker Hub
* Nginx

---

# 📁 Project Structure

```
aws-docker/
│── index.html
│── styles.css
│── script.js
│── Dockerfile
│── .github/
│    └── workflows/
│         └── deploy.yml
```

---

# 🐳 Docker Setup

## Dockerfile

```Dockerfile
FROM nginx:latest
COPY . /usr/share/nginx/html
EXPOSE 80
```

## Build Image

```bash
docker build -t my-static-site .
```

## Run Container

```bash
docker run -d -p 8080:80 --name my-container my-static-site
```

---

# ☁️ AWS EC2 Setup

## Connect to EC2

```bash
ssh -i your-key.pem ubuntu@your-ip
```

## Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
```

---

# 🔐 GitHub Secrets

Add the following secrets in your repository:

| Secret Name     | Description           |
| --------------- | --------------------- |
| DOCKER_USERNAME | Docker Hub username   |
| DOCKER_PASSWORD | Docker Hub password   |
| EC2_HOST        | EC2 Public IP         |
| EC2_SSH_KEY     | EC2 .pem file content |

---

# ⚙️ GitHub Actions Workflow

Path: `.github/workflows/deploy.yml`

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Login to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build Docker Image
      run: docker build -t ${{ secrets.DOCKER_USERNAME }}/my-static-site:latest .

    - name: Push Docker Image
      run: docker push ${{ secrets.DOCKER_USERNAME }}/my-static-site:latest

    - name: Deploy to EC2
      uses: appleboy/ssh-action@v0.1.6
      with:
        host: ${{ secrets.EC2_HOST }}
        username: ubuntu
        key: ${{ secrets.EC2_SSH_KEY }}
        script: |
          docker pull ${{ secrets.DOCKER_USERNAME }}/my-static-site:latest
          docker stop my-container || true
          docker rm my-container || true
          docker run -d -p 8080:80 --name my-container ${{ secrets.DOCKER_USERNAME }}/my-static-site:latest
```

---

# 🌐 Access Website

```
http://<EC2-PUBLIC-IP>:8080
```

---

# 🧠 Key Learnings

* How Docker containerizes applications
* Difference between local deployment vs container deployment
* How CI/CD pipelines automate deployment
* Using Docker Hub as a container registry
* SSH-based remote deployment using GitHub Actions
* AWS Security Groups and networking basics

---

# ⚠️ Common Issues & Fixes

### Port not accessible

* Ensure port 8080 is open in AWS Security Group

### SSH connection failed

* Check EC2_SSH_KEY format
* Ensure port 22 is open

### Docker permission error

```bash
sudo chmod 666 /var/run/docker.sock
```

---

# 🚀 Future Improvements

* Add custom domain with Route 53
* Enable HTTPS using CloudFront
* Use AWS ECR instead of Docker Hub
* Add Load Balancer
* Move to Kubernetes (EKS)

---

# 📌 Author

Shubham Gupta
DevOps Learner 🚀

---

# ⭐ If you like this project

Give it a ⭐ on GitHub and use it as your DevOps starter template!

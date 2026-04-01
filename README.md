# 🚀 AWS + Docker + GitHub Actions CI/CD Project

This project demonstrates a **complete end-to-end DevOps pipeline** to deploy a static website using Docker, GitHub Actions, Docker Hub, and AWS EC2.

---

# 📌 Project Overview

This project automates deployment using CI/CD:

```text
GitHub Push
   ↓
GitHub Actions (Build Docker Image)
   ↓
Push Image to Docker Hub
   ↓
EC2 pulls image
   ↓
Runs container (Nginx server)
   ↓
Website Live 🚀
```

---

# 🧱 Tech Stack

* 🐳 Docker (Containerization)
* ☁️ AWS EC2 (Ubuntu Server)
* 🔄 GitHub Actions (CI/CD Automation)
* 📦 Docker Hub (Image Registry)
* 🌐 Nginx (Web Server)

---

# 📁 Project Structure

```text
aws-docker/
│── index.html
│── style.css
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

---

## Build Image (Local)

```bash
docker build -t my-static-site .
```

---

## Run Container (Local Test)

```bash
docker run -d -p 8080:80 my-static-site
```

Open:

```text
http://localhost:8080
```

---

# ☁️ AWS EC2 Setup (Ubuntu)

## Connect to EC2

```bash
ssh -i your-key.pem ubuntu@your-ec2-ip
```

---

## Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

---

## Fix Docker Permission (IMPORTANT)

```bash
sudo usermod -aG docker ubuntu
sudo chmod 666 /var/run/docker.sock
```

👉 Logout & login again:

```bash
exit
ssh -i your-key.pem ubuntu@your-ip
```

---

# 🔐 GitHub Secrets

Add these in **Repo → Settings → Secrets → Actions**

| Secret Name     | Description              |
| --------------- | ------------------------ |
| DOCKER_USERNAME | Docker Hub username      |
| DOCKER_PASSWORD | Docker Hub password      |
| EC2_HOST        | EC2 Public IP            |
| EC2_SSH_KEY     | Full `.pem` file content |

---

# ⚙️ GitHub Actions Workflow

File: `.github/workflows/deploy.yml`

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

# 🚀 Manual Deployment (Important Learning)

If CI/CD fails, run manually on EC2:

```bash
docker pull shubh0264/my-static-site
docker stop my-container || true
docker rm my-container || true
docker run -d -p 8080:80 --name my-container shubh0264/my-static-site
```

---

# 🌐 Access Website

```text
http://<EC2-PUBLIC-IP>:8080
```

Example:

```text
http://13.201.xx.xxx:8080
```

---

# 🔓 AWS Security Group Rules

| Type       | Port |
| ---------- | ---- |
| SSH        | 22   |
| HTTP       | 80   |
| Custom TCP | 8080 |

Source: `0.0.0.0/0`

---

# 🧠 Key Learnings

* Docker containerization and image creation
* Difference between local image vs Docker Hub image
* CI/CD pipeline using GitHub Actions
* Using Docker Hub as a central registry
* Deploying containers on AWS EC2
* Debugging real-world DevOps issues (ports, permissions, SSH)

---

# ⚠️ Common Errors & Fixes

## ❌ Permission Denied

```bash
sudo usermod -aG docker ubuntu
exit
```

---

## ❌ Image Not Found

Wrong:

```bash
docker run my-static-site
```

Correct:

```bash
docker run shubh0264/my-static-site
```

---

## ❌ Site Not Opening

* Check port 8080 in Security Group
* Use correct URL:

```text
http://EC2-IP:8080
```

---

## ❌ SSH Connection Failed

* Check `.pem` file format
* Ensure port 22 open

---

# 🔥 Future Improvements

* Add custom domain (Route 53)
* Enable HTTPS (CloudFront / SSL)
* Use AWS ECR instead of Docker Hub
* Add Load Balancer
* Deploy using Kubernetes (EKS)

---

# 👨‍💻 Author

Shubham Gupta
DevOps Learner 🚀

---

# ⭐ If you like this project

Give it a ⭐ on GitHub!

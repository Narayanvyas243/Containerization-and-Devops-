# CI/CD Pipeline using Jenkins, GitHub, and Docker Hub

## Project Overview

This project demonstrates the implementation of a complete CI/CD pipeline using Jenkins, GitHub, and Docker Hub. The pipeline automates the process of building a Docker image from application source code and pushing it to Docker Hub.

The system ensures that every code update can be built and deployed efficiently with minimal manual effort.

---

## Objective

* To understand Continuous Integration and Continuous Deployment (CI/CD)
* To automate the build process using Jenkins
* To integrate GitHub with Jenkins using webhooks
* To build Docker images from source code
* To push Docker images to Docker Hub securely

---

## Technologies Used

* Jenkins (running inside Docker container)
* GitHub (source code repository)
* Docker (containerization platform)
* Docker Hub (image registry)
* Ngrok (for webhook tunneling)
* WSL (Linux environment on Windows)

---

## Project Structure

my-app/
├── app.py
├── requirements.txt
├── Dockerfile
├── Jenkinsfile

---

## Application Code

### app.py

from flask import Flask
app = Flask(**name**)

@app.route("/")
def home():
return "Hello from CI/CD Pipeline!"

app.run(host="0.0.0.0", port=80)

---

### requirements.txt

flask

---

## Dockerfile

FROM python:3.10-slim

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

EXPOSE 80
CMD ["python", "app.py"]

---

## Jenkinsfile

pipeline {
agent any

```
environment {  
    IMAGE_NAME = "narayanvyas243/myapp"  
}  

stages {  

    stage('Clone Source') {  
        steps {  
            git branch: 'main', url: 'https://github.com/Narayanvyas243/my-app.git'  
        }  
    }  

    stage('Build Docker Image') {  
        steps {  
            sh 'docker build -t $IMAGE_NAME:latest .'  
        }  
    }  

    stage('Login to Docker Hub') {  
        steps {  
            withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {  
                sh 'echo $DOCKER_TOKEN | docker login -u narayanvyas243 --password-stdin'  
            }  
        }  
    }  

    stage('Push to Docker Hub') {  
        steps {  
            sh 'docker push $IMAGE_NAME:latest'  
        }  
    }  
}  
```

}

---

## Jenkins Setup

Jenkins was deployed using Docker with persistent storage and Docker socket access:

docker run -d --name jenkins
-p 8080:8080 -p 50000:50000
-v jenkins_home:/var/jenkins_home
-v /var/run/docker.sock:/var/run/docker.sock
-u root myjenkins-blueocean:2.541.3-1

This setup allows Jenkins to interact directly with the Docker engine on the host system.

---

## CI/CD Workflow

1. Developer pushes code to GitHub repository
2. GitHub sends webhook request to Jenkins
3. Jenkins pulls the latest code
4. Docker image is built using Dockerfile
5. Jenkins logs into Docker Hub securely
6. Docker image is pushed to Docker Hub

---

## Webhook Setup

Since Jenkins runs locally, ngrok is used to expose it to the internet:

ngrok http 8080

The webhook URL used in GitHub:

https://<ngrok-url>/github-webhook/

The trigger "GitHub hook trigger for GITScm polling" is enabled in Jenkins.

---

## Errors Faced and Solutions

### 1. Branch Not Found Error

Error: couldn't find remote ref refs/heads/master

Cause: The repository used 'main' instead of 'master'.

Solution: Updated branch configuration in Jenkins and Jenkinsfile to 'main'.

---

### 2. Jenkinsfile Not Found

Error: Unable to find Jenkinsfile

Cause: Jenkinsfile was missing or incorrectly named.

Solution: Created Jenkinsfile in root directory with exact name and correct case.

---

### 3. Docker Not Accessible from Jenkins

Error: failed to connect to Docker API

Cause: Jenkins container had no access to Docker daemon.

Solution: Mounted Docker socket using:
-v /var/run/docker.sock:/var/run/docker.sock

---

### 4. Docker Permission Denied

Error: permission denied while accessing docker.sock

Solution:
sudo chmod 666 /var/run/docker.sock

---

### 5. Credentials Not Found

Error: Could not find credentials entry with ID 'dockerhub-token'

Cause: Credentials not configured correctly or wrong scope.

Solution:

* Created credential in Jenkins
* Type: Secret Text
* ID: dockerhub-token
* Scope: Global

---

### 6. Webhook Not Triggering

Cause: GitHub cannot access localhost directly.

Solution:

* Used ngrok to expose Jenkins
* Updated webhook URL
* Enabled webhook trigger in Jenkins

---

### 7. Ngrok URL Expired

Cause: Ngrok generates a new URL on restart.

Solution:

* Restart ngrok
* Update webhook URL in GitHub

---

## Final Result

* Jenkins successfully builds Docker image
* Image is pushed to Docker Hub
* CI/CD pipeline works as expected
* Automation can be achieved using webhooks

---

## Key Learnings

* Practical understanding of CI/CD pipelines
* Jenkins pipeline configuration
* Docker image creation and management
* Secure handling of credentials
* Debugging real-world DevOps issues
* Integration of multiple tools in a workflow

---

## Conclusion

This project demonstrates a complete end-to-end CI/CD pipeline using industry-standard tools. It provides hands-on experience in automation, containerization, and deployment, which are essential skills in modern DevOps practices.

---

#  Experiment 4: Docker Essentials

## 📌 Objective

This experiment demonstrates:

- Writing a Dockerfile
- Using `.dockerignore`
- Building Docker images
- Running containers
- Multi-stage builds
- Tagging images
- Publishing to Docker Hub
- Containerizing Python (Flask) and Node.js applications

---

#  Part 1: Python Flask Application

![Screenshot](Screenshot%202026-02-20%20102415.png)

## 📁 Project Structure

```
my-flask-app/
│
├── app.py
├── requirements.txt
├── Dockerfile
└── .dockerignore
```

---

## 📝 app.py

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Docker!"

@app.route('/health')
def health():
    return "OK"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
![Screenshot](Screenshot%202026-02-20%20102442.png)

---

## 📄 requirements.txt
![Screenshot](Screenshot%202026-02-20%20102556.png)
```
Flask==2.3.3
```
![Screenshot](Screenshot%202026-02-20%20102603.png)

---

##  Dockerfile
![Screenshot](Screenshot%202026-02-20%20102637.png)
```dockerfile
# Use Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY app.py .

# Expose port
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]
```
![Screenshot](Screenshot%202026-02-20%20102937.png)

---

# 📂 Part 2: .dockerignore

## 📄 .dockerignore
![Screenshot](Screenshot%202026-02-20%20103058.png)
```
__pycache__/
*.pyc
*.pyo
*.pyd

.env
.venv
env/
venv/

.vscode/
.idea/

.git/
.gitignore

.DS_Store
Thumbs.db

*.log
logs/

tests/
test_*.py
```
![Screenshot](Screenshot%202026-02-20%20103119.png)
### Why use .dockerignore?

- Reduces image size  
- Improves build speed  
- Improves security  
- Prevents unnecessary files from being copied  

---

# 🏗 Part 3: Building Docker Images

## Build Image

```bash
docker build -t my-flask-app .
```
![Screenshot](Screenshot%202026-02-20%20103307.png)
## Build with Version Tag

```bash
docker build -t my-flask-app:1.0 .
```
![Screenshot](Screenshot%202026-02-20%20103504.png)
## Multiple Tags

```bash
docker build -t my-flask-app:latest -t my-flask-app:1.0 .
```
![Screenshot](Screenshot%202026-02-20%20103536.png)
## View Images

```bash
docker images
```
![Screenshot](Screenshot%202026-02-20%20103345.png)
## Inspect Image

```bash
docker inspect my-flask-app
```
![Screenshot](Screenshot%202026-02-20%20103821.png)
![Screenshot](Screenshot%202026-02-20%20110739.png)

---

#  Part 4: Running Containers

## Run Container

```bash
docker run -d -p 5000:5000 --name flask-container my-flask-app
```
![Screenshot](Screenshot%202026-02-20%20110820.png)
## Test Application
![Screenshot](Screenshot%202026-02-20%20110853.png)
```bash
curl http://localhost:5000
```
![Screenshot](Screenshot%202026-02-20%20110950.png)
## View Running Containers

```bash
docker ps
```
![Screenshot](Screenshot%202026-02-20%20111017.png)
## View Logs

```bash
docker logs flask-container
```
![Screenshot](Screenshot%202026-02-20%20111052.png)
## Stop Container

```bash
docker stop flask-container
```

## Remove Container

```bash
docker rm flask-container
```

## Force Remove

```bash
docker rm -f flask-container
```
![Screenshot](Screenshot%202026-02-20%20111139.png)

---

# 🏗 Part 5: Multi-stage Build
![Screenshot](Screenshot%202026-02-20%20111253.png)
## 📄 Dockerfile.multistage

```dockerfile
# STAGE 1: Builder
FROM python:3.9-slim AS builder

WORKDIR /app
COPY requirements.txt .

RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

RUN pip install --no-cache-dir -r requirements.txt

# STAGE 2: Runtime
FROM python:3.9-slim

WORKDIR /app

COPY --from=builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

COPY app.py .

RUN useradd -m -u 1000 appuser
USER appuser

EXPOSE 5000

CMD ["python", "app.py"]
```

## Build Multi-stage Image

```bash
docker build -f Dockerfile.multistage -t flask-multistage .
```

---
#  Single-Stage Dockerfile

```
# Use official Python base image
FROM python:3.9-slim

# Set working directory inside container
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application source code
COPY app.py .

# Expose application port
EXPOSE 5000

# Start the Flask application
CMD ["python", "app.py"]
```
![Screenshot](Screenshot%202026-02-20%20220019.png)
---

# 🏗 Step 3: Build Docker Image

```
docker build -t flask-single-stage .
```
![Screenshot](Screenshot%202026-02-20%20220026.png)

# 📊 Image Size Check

```
docker images | grep flask
```
![Screenshot](Screenshot%202026-02-20%20220045.png)
![Screenshot](Screenshot%202026-02-20%20220123.png)

---

#  Part 6: Publishing to Docker Hub

## Login

```bash
docker login
```

## Tag Image

```bash
docker tag my-flask-app:latest username/my-flask-app:1.0
docker tag my-flask-app:latest username/my-flask-app:latest
```

## Push Image

```bash
docker push username/my-flask-app:1.0
docker push username/my-flask-app:latest
```
![Screenshot](Screenshot%202026-02-20%20221916.png)

## Pull on Another Machine

```bash
docker pull username/my-flask-app:latest
```
![WhatsApp](WhatsApp%20Image%202026-02-20%20at%2010.41.56%20PM.jpeg)

## Run Pulled Image

```bash
docker run -d -p 5000:5000 username/my-flask-app:latest
```
![WhatsApp](WhatsApp%20Image%202026-02-20%20at%2010.41.57%20PM%20(1).jpeg)
![WhatsApp](WhatsApp%20Image%202026-02-20%20at%2010.41.57%20PM.jpeg)

---

#  Part 7: Node.js Example
![Screenshot](Screenshot%202026-02-20%20224556.png)
## 📁 Project Structure

```
my-node-app/
│
├── app.js
├── package.json
└── Dockerfile
```

---

## 📝 app.js

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
    res.send('Hello from Node.js Docker!');
});

app.get('/health', (req, res) => {
    res.json({ status: 'healthy' });
});

app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});
```

---

## 📄 package.json

```json
{
  "name": "node-docker-app",
  "version": "1.0.0",
  "main": "app.js",
  "dependencies": {
    "express": "^4.18.2"
  }
}
```
![Screenshot](Screenshot%202026-02-20%20224630.png)

---

## 🐳 Node.js Dockerfile
![Screenshot](Screenshot%202026-02-20%20224723.png)
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --only=production

COPY app.js .

EXPOSE 3000

CMD ["node", "app.js"]
```

## Build & Run

```bash
docker build -t my-node-app .
docker run -d -p 3000:3000 --name node-container my-node-app
```
![Screenshot](Screenshot%202026-02-20%20225006.png)
![Screenshot](Screenshot%202026-02-20%20225029.png)
## Test

```bash
curl http://localhost:3000
```
![Screenshot](Screenshot%202026-02-20%20225105.png)
![Screenshot](Screenshot%202026-02-20%20225235.png)

---

# 📋 Essential Docker Commands

| Command | Purpose |
|----------|----------|
| docker build | Build image |
| docker run | Run container |
| docker ps | List containers |
| docker images | List images |
| docker tag | Tag image |
| docker login | Login to Docker Hub |
| docker push | Push image |
| docker pull | Pull image |
| docker logs | View logs |
| docker exec -it | Access container |
| docker rm | Remove container |
| docker rmi | Remove image |

---

# 🔁 Development Workflow

1. Create Dockerfile and .dockerignore  
2. Build image  
3. Test locally  
4. Tag image  
5. Push to Docker Hub  
6. Deploy in production  

---

# 🧹 Cleanup Commands

```bash
docker container prune
docker image prune
docker system prune -a
```

---

#  Key Takeaways

- Dockerfile defines image creation
- .dockerignore improves security and performance
- Multi-stage builds reduce image size
- Tagging helps version control
- Docker Hub enables image distribution
- Always test locally before publishing

---

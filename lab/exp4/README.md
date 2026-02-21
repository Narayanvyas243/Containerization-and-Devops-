# 🐳 Experiment 4: Docker Essentials

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

# 📦 Part 1: Python Flask Application

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

---

## 📄 requirements.txt

```
Flask==2.3.3
```

---

## 🐳 Dockerfile

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

---

# 📂 Part 2: .dockerignore

## 📄 .dockerignore

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

## Build with Version Tag

```bash
docker build -t my-flask-app:1.0 .
```

## Multiple Tags

```bash
docker build -t my-flask-app:latest -t my-flask-app:1.0 .
```

## View Images

```bash
docker images
```

## Inspect Image

```bash
docker inspect my-flask-app
```

---

# 🚀 Part 4: Running Containers

## Run Container

```bash
docker run -d -p 5000:5000 --name flask-container my-flask-app
```

## Test Application

```bash
curl http://localhost:5000
```

## View Running Containers

```bash
docker ps
```

## View Logs

```bash
docker logs flask-container
```

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

---

# 🏗 Part 5: Multi-stage Build

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

# 🌍 Part 6: Publishing to Docker Hub

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

## Pull on Another Machine

```bash
docker pull username/my-flask-app:latest
```

## Run Pulled Image

```bash
docker run -d -p 5000:5000 username/my-flask-app:latest
```

---

# 🟢 Part 7: Node.js Example

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

---

## 🐳 Node.js Dockerfile

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

## Test

```bash
curl http://localhost:3000
```

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

# ✅ Key Takeaways

- Dockerfile defines image creation
- .dockerignore improves security and performance
- Multi-stage builds reduce image size
- Tagging helps version control
- Docker Hub enables image distribution
- Always test locally before publishing

---

# 👨‍🏫 Instructor

Designed by **Dr. Prateek Raj Gautam**  
Course: Containerization and DevOps Lab  
Platform: mGeek.in

---

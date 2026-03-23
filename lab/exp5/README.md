#  Experiment 5: Docker - Volumes, Environment Variables, Monitoring & Networks

##  Objective

To understand and implement:

* Docker Volumes (data persistence)
* Environment Variables in containers
* Docker Monitoring tools
* Docker Networking concepts
* Multi-container application setup

---

#  Part 1: Docker Volumes (Persistent Storage)

## 🔹 Problem: Container Data is Ephemeral

```bash
docker run -it --name test-container ubuntu /bin/bash

# Inside container
echo "Hello World" > /data/message.txt
cat /data/message.txt
exit

docker start test-container
docker exec test-container cat /data/message.txt
#  Data lost
```
![Screenshot](./Screenshot%202026-03-18%20004246.png)**

---

## 🔹 Solution: Docker Volumes

### 1️⃣ Anonymous Volume

```bash
docker run -d -v /app/data --name web1 nginx
docker volume ls
docker inspect web1
```
![Screenshot](./Screenshot%202026-03-20%20110925.png)

---

### 2️⃣ Named Volume

```bash
docker volume create mydata

docker run -d -v mydata:/app/data --name web2 nginx

docker volume ls
docker volume inspect mydata
```

---

### 3️⃣ Bind Mount

```bash
mkdir ~/myapp-data

docker run -d -v ~/myapp-data:/app/data --name web3 nginx

echo "From Host" > ~/myapp-data/host-file.txt

docker exec web3 cat /app/data/host-file.txt
```
![Screenshot](./Screenshot%202026-03-20%20110943.png)

---

## 🔹 Practical Example: MySQL with Volume

```bash
docker run -d \
  --name mysql-db \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8.0

docker stop mysql-db
docker rm mysql-db

docker run -d \
  --name new-mysql \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8.0
```
![Screenshot](./Screenshot%202026-03-20%20112924.png)
![Screenshot](./Screenshot%202026-03-20%20113458.png)

---

## 🔹 Nginx Config with Bind Mount

```bash
mkdir ~/nginx-config

echo 'server {
    listen 80;
    server_name localhost;
    location / {
        return 200 "Hello from mounted config!";
    }
}' > ~/nginx-config/nginx.conf

docker run -d \
  --name nginx-custom \
  -p 8080:80 \
  -v ~/nginx-config/nginx.conf:/etc/nginx/conf.d/default.conf \
  nginx

curl http://localhost:8080
```

---

## 🔹 Volume Commands

```bash
docker volume ls
docker volume create app-volume
docker volume inspect app-volume
docker volume prune
docker volume rm volume-name
docker cp file.txt container:/path
```

---

#  Part 2: Environment Variables

## 🔹 Using -e flag

```bash
docker run -d \
  --name app1 \
  -e DATABASE_URL="postgres://user:pass@db:5432/mydb" \
  -e DEBUG=true \
  -p 3000:3000 \
  my-node-app
```

---

## 🔹 Using .env file

```bash
echo "DATABASE_HOST=localhost" > .env
echo "DATABASE_PORT=5432" >> .env
echo "API_KEY=secret123" >> .env

docker run -d --env-file .env --name app2 my-app
```
![Screenshot](./Screenshot%202026-03-20%20113641.png)
![Screenshot](./Screenshot%202026-03-20%20114015.png)

---

## 🔹 Flask App Example

### app.py

```python
import os
from flask import Flask

app = Flask(__name__)

db_host = os.environ.get('DATABASE_HOST', 'localhost')
debug_mode = os.environ.get('DEBUG', 'false').lower() == 'true'
api_key = os.environ.get('API_KEY')

@app.route('/config')
def config():
    return {
        'db_host': db_host,
        'debug': debug_mode,
        'has_api_key': bool(api_key)
    }

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port, debug=debug_mode)
```

---

### Dockerfile

```dockerfile
FROM python:3.9-slim

ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY app.py .

ENV PORT=5000
ENV DEBUG=false

EXPOSE 5000

CMD ["python", "app.py"]
```

---

## 🔹 Run Flask App

```bash
docker build -t flask-app .

docker run -d \
  --name flask-app \
  -p 5000:5000 \
  -e DATABASE_HOST="prod-db" \
  -e DEBUG=true \
  -e PORT=5000 \
  flask-app

curl http://localhost:5000/config
```

---

#  Part 3: Docker Monitoring

## 🔹 Stats

```bash
docker stats
docker stats --no-stream
```
![Screenshot](./Screenshot%202026-03-20%20114142.png)

---

## 🔹 Processes

```bash
docker top container-name
```
![Screenshot](./Screenshot%202026-03-20%20114300.png)

---

## 🔹 Logs

```bash
docker logs container-name
docker logs -f container-name
docker logs --tail 50 container-name
```
![Screenshot](./Screenshot%202026-03-20%20114326.png)

---

## 🔹 Inspect

```bash
docker inspect container-name
docker inspect --format='{{.State.Status}}' container-name
```
![Screenshot](./Screenshot%202026-03-20%20114350.png)

---

## 🔹 Events

```bash
docker events
docker events --filter 'event=start'
```
![Screenshot](./Screenshot%202026-03-20%20114416.png)
![Screenshot](./Screenshot%202026-03-20%20114431.png)

---

## 🔹 Monitoring Script

```bash
#!/bin/bash
echo "=== Docker Monitoring Dashboard ==="

docker ps
docker stats --no-stream
docker events --since '5m'
docker system df
```

---

#  Part 4: Docker Networks

## 🔹 List Networks

```bash
docker network ls
```
![Screenshot](./Screenshot%202026-03-20%20114915.png)

---

## 🔹 Bridge Network

```bash
docker network create my-network

docker run -d --name web1 --network my-network nginx
docker run -d --name web2 --network my-network nginx

docker exec web1 curl http://web2
```
![Screenshot](./Screenshot%202026-03-20%20114945.png)

---

## 🔹 Host Network

```bash
docker run -d --network host nginx
```
![Screenshot](./Screenshot%202026-03-20%20115210.png)
![Screenshot](./Screenshot%202026-03-20%20115254.png)

---

## 🔹 None Network

```bash
docker run -d --network none alpine sleep 3600
```

---

## 🔹 Network Commands

```bash
docker network create app-network
docker network connect app-network container
docker network disconnect app-network container
docker network rm app-network
docker network prune
```
![Screenshot](./Screenshot%202026-03-20%20115814.png)

---

#  Multi-Container Example

```bash
docker network create app-network

docker run -d \
  --name postgres-db \
  --network app-network \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15

docker run -d \
  --name web-app \
  --network app-network \
  -p 8080:3000 \
  -e DATABASE_URL="postgres://postgres:secret@postgres-db:5432/mydb" \
  node-app
```
![Screenshot](./Screenshot%202026-03-20%20121500.png)
![Screenshot](./Screenshot%202026-03-20%20121511.png)

---

#  Part 5: Real-World Example

```bash
docker network create myapp-network

docker run -d \
  --name postgres \
  --network myapp-network \
  -e POSTGRES_PASSWORD=mysecretpassword \
  -e POSTGRES_DB=mydatabase \
  -v postgres-data:/var/lib/postgresql/data \
  postgres:15

docker run -d \
  --name redis \
  --network myapp-network \
  -v redis-data:/data \
  redis:7-alpine

docker run -d \
  --name flask-app \
  --network myapp-network \
  -p 5000:5000 \
  -v $(pwd)/app:/app \
  -e DATABASE_URL="postgresql://postgres:mysecretpassword@postgres:5432/mydatabase" \
  -e REDIS_URL="redis://redis:6379" \
  flask-app
```

---

#  Cheatsheet

### Volumes

```bash
docker volume create name
docker run -v name:/path
```

### Env Variables

```bash
docker run -e VAR=value
docker run --env-file .env
```

### Monitoring

```bash
docker stats
docker logs -f container
docker inspect container
```

### Networks

```bash
docker network create name
docker run --network name
```

---

#  Cleanup

```bash
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
docker volume prune -f
docker network prune -f
docker image prune -f
```

---

#  Key Takeaways

* Volumes store persistent data
* Environment variables configure apps
* Monitoring helps debugging
* Networks connect containers securely
* Use named volumes in production
* Use `.env` for configuration

---

#  Conclusion

This experiment demonstrates how Docker enables:

* Scalable applications
* Persistent storage
* Configurable environments
* Multi-container architectures

👉 These are **real industry-level concepts** used in DevOps and Cloud Engineering.

---


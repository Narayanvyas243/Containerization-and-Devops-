
#  Experiment 6: Docker Run vs Docker Compose

##  Objective

To understand the difference between `docker run` (imperative approach) and `Docker Compose` (declarative approach), and to deploy single-container and multi-container applications.

---

#  PART A – THEORY

## 🔹 Docker Run (Imperative)

* Uses command-line instructions
* Requires manual flags for:

  * Ports (`-p`)
  * Volumes (`-v`)
  * Environment variables (`-e`)
  * Network (`--network`)
* Example:

```bash
docker run -d --name my-nginx -p 8080:80 nginx:alpine
```
![Screenshot%201](Screenshot%202026-03-25%20011115.png)

---

## 🔹 Docker Compose (Declarative)

* Uses YAML file (`docker-compose.yml`)
* Defines services in structured format
* Run using:

```bash
docker compose up -d
```

---

## 🔹 Mapping

| Docker Run | Docker Compose |
| ---------- | -------------- |
| -p         | ports          |
| -v         | volumes        |
| -e         | environment    |
| --name     | container_name |
| --network  | networks       |
| --restart  | restart        |

---

## 🔹 Advantages of Docker Compose

* Easy multi-container setup
* Reproducible configuration
* Version control friendly
* One-command deployment

---

#  PART B – PRACTICAL TASK

##  Task 1: Single Container (Nginx)

### 🔹 Step 1: Setup

```bash
mkdir html
echo Hello Docker > html/index.html
```

![Screenshot%202](Screenshot%202026-03-25%20011124.png)

---

### 🔹 Using Docker Run

```bash
docker run -d --name lab-nginx -p 8081:80 -v ${PWD}/html:/usr/share/nginx/html nginx:alpine
```
![Screenshot%204](Screenshot%202026-03-25%20012551.png)

Verify:

```bash
docker ps
```
![Screenshot%205](Screenshot%202026-03-25%20012559.png)

Access:

```
http://localhost:8081
```

Stop:

```bash
docker stop lab-nginx
docker rm lab-nginx
```

---

### 🔹 Using Docker Compose

**docker-compose.yml**

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    container_name: lab-nginx
    ports:
      - "8081:80"
    volumes:
      - ./html:/usr/share/nginx/html
```
![Screenshot%203](Screenshot%202026-03-25%20012232.png)

Run:

```bash
docker compose up -d
```
![Screenshot%206](Screenshot%202026-03-25%20012705.png)

Stop:

```bash
docker compose down
```
![Screenshot%207](Screenshot%202026-03-25%20012717.png)
![Screenshot%208](Screenshot%202026-03-25%20012734.png)

---

##  Task 2: Multi-Container (WordPress + MySQL)

### 🔹 Using Docker Run

Create network:

```bash
docker network create wp-net
```
![Screenshot%209](Screenshot%202026-03-25%20013048.png)

Run MySQL:

```bash
docker run -d --name mysql --network wp-net -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=wordpress mysql:5.7
```

Run WordPress:

```bash
docker run -d --name wordpress --network wp-net -p 8082:80 -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_PASSWORD=secret wordpress:latest
```
![Screenshot%2010](Screenshot%202026-03-25%20013422.png)

Access:

```
http://localhost:8082
```

Cleanup:

```bash
docker stop wordpress mysql
docker rm wordpress mysql
docker network rm wp-net
```
![Screenshot%2011](Screenshot%202026-03-25%20013557.png)

---

### 🔹 Using Docker Compose

**docker-compose.yml**

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: wordpress
    volumes:
      - mysql_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    ports:
      - "8082:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_PASSWORD: secret
    depends_on:
      - mysql

volumes:
  mysql_data:
```
![Screenshot%2012](Screenshot%202026-03-25%20013633.png)

Run:

```bash
docker compose up -d
```
![Screenshot%2013](Screenshot%202026-03-25%20013850.png)

Stop:

```bash
docker compose down -v
```
![Screenshot%2014](Screenshot%202026-03-25%20013902.png)

---

#  PART C – CONVERSION TASKS

##  Task 3: Convert Docker Run to Compose

### Given:

```bash
docker run -d \
  --name webapp \
  -p 5000:5000 \
  -e APP_ENV=production \
  -e DEBUG=false \
  --restart unless-stopped \
  node:18-alpine
```


### Solution:

```yaml
version: '3.8'

services:
  webapp:
    image: node:18-alpine
    container_name: webapp
    ports:
      - "5000:5000"
    environment:
      APP_ENV: production
      DEBUG: false
    restart: unless-stopped
```
![Screenshot%2015](Screenshot%202026-03-25%20013936.png)

---

##  Problem 2: Volume + Network

```yaml
version: '3.8'

services:
  postgres-db:
    image: postgres:15
    container_name: postgres-db
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - app-net

  backend:
    image: python:3.11-slim
    container_name: backend
    ports:
      - "8000:8000"
    environment:
      DB_HOST: postgres-db
      DB_USER: admin
      DB_PASS: secret
    depends_on:
      - postgres-db
    networks:
      - app-net

volumes:
  pgdata:

networks:
  app-net:
```

---

#  PART D – BUILD USING DOCKERFILE

## 🔹 Step 1: app.js

```js
const http = require('http');

http.createServer((req, res) => {
  res.end("Docker Compose Build Lab");
}).listen(3000);
```
![Screenshot%2019](Screenshot%202026-03-25%20014324.png)

---

## 🔹 Step 2: Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY app.js .

EXPOSE 3000

CMD ["node", "app.js"]
```
![Screenshot%2020](Screenshot%202026-03-25%20014349.png)

---

## 🔹 Step 3: docker-compose.yml

```yaml
version: '3.8'

services:
  nodeapp:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: custom-node-app
    ports:
      - "3000:3000"
```
![Screenshot%2021](Screenshot%202026-03-25%20014513.png)

---

## 🔹 Run

```bash
docker compose up --build -d
```
![Screenshot%2022](Screenshot%202026-03-25%20014731.png)
![Screenshot%2023](Screenshot%202026-03-25%20014745.png)
Access:

```
http://localhost:3000
```

---

### Difference: docker run vs compose

* docker run → manual commands
* docker compose → YAML-based configuration

###  image vs build

* image → prebuilt image
* build → create custom image

###  depends_on

* Ensures service startup order

---

# Conclusion

* Learned Docker Run vs Compose
* Deployed single & multi-container apps
* Used volumes, networks, environment variables
* Built custom image using Dockerfile

---


# Experiment 11

**Title:** Orchestration using Docker Compose & Docker Swarm (Continuation of Experiment 6)

---

## Aim

To study container orchestration and deploy a multi-container application using Docker Swarm with scaling and self-healing features.

---

## Prerequisites

* Docker installed and running
* Basic knowledge of Docker and Docker Compose
* docker-compose.yml file (WordPress + MySQL)

---

## Concept

From previous experiment, Docker Compose can run multiple containers but only on a single machine and without advanced features.
Docker Swarm provides orchestration which includes:

* Scaling of containers
* Self-healing (automatic recovery)
* Load balancing
* Multi-node support

Progression:
docker run → docker compose → docker swarm → kubernetes

---

## Step 1: Stop Previous Containers

Command used:
docker compose down -v
docker ps
![ss](./Screenshot%202026-04-24%20225210.png)
Observation:
All running containers were stopped and removed successfully.

---

## Step 2: Create docker-compose.yml File

Command used:
nano docker-compose.yml
![ss](./Screenshot%202026-04-24%20225145.png)
Content of file:

version: '3.9'

services:
db:
image: mysql:5.7
restart: always
environment:
MYSQL_ROOT_PASSWORD: rootpass
MYSQL_DATABASE: wordpress
MYSQL_USER: wpuser
MYSQL_PASSWORD: wppass
volumes:
- db_data:/var/lib/mysql

wordpress:
image: wordpress:latest
depends_on:
- db
ports:
- "8080:80"
restart: always
environment:
WORDPRESS_DB_HOST: db:3306
WORDPRESS_DB_USER: wpuser
WORDPRESS_DB_PASSWORD: wppass
WORDPRESS_DB_NAME: wordpress
volumes:
- wp_data:/var/www/html

volumes:
db_data:
wp_data:

Observation:
Compose file successfully created for WordPress and MySQL services.

---

## Step 3: Initialize Docker Swarm

Command used:
docker swarm init

Verification:
docker node ls
![ss](./Screenshot%202026-04-24%20225238.png)
Observation:
System became manager node and status was shown as Ready, Active, Leader.

---

## Step 4: Deploy Stack

Command used:
docker stack deploy -c docker-compose.yml wpstack

Observation:
Services wpstack_db and wpstack_wordpress were created successfully.

---

## Step 5: Verify Deployment

Commands used:
docker service ls
docker service ps wpstack_wordpress
docker ps
![ss](./Screenshot%202026-04-24%20225250.png)
![ss](./Screenshot%202026-04-24%20225303.png)
Observation:
Services were running and containers were managed by Swarm.

---

## Step 6: Access Application

URL used:
http://localhost:8080
![ss](./Screenshot%202026-04-24%20225400.png)
Observation:
WordPress setup page opened successfully in browser.

---

## Step 7: Scale the Application

Command used:
docker service scale wpstack_wordpress=3
![ss](./Screenshot%202026-04-24%20225455.png)
Verification:
docker service ls
docker ps

Observation:
Three WordPress containers were running simultaneously.
Swarm automatically distributed traffic among them.

---

## Step 8: Test Self-Healing

Commands used:
docker ps
docker kill <container-id>
docker service ps wpstack_wordpress
![ss](./Screenshot%202026-04-24%20225552.png)
Observation:
After killing one container, a new container was automatically created.
Total replicas remained 3.
This confirmed self-healing behavior.

---

## Step 9: Remove Stack

Command used:
docker stack rm wpstack

Verification:
docker service ls
docker ps

Observation:
All services and containers were removed successfully.

---

## Step 10: Leave Swarm (for repeat execution)

Command used:
docker swarm leave --force

Observation:
Swarm mode disabled and system returned to normal Docker mode.

---

## Problems Faced

* Port 8080 was sometimes already in use due to previous containers
* Swarm initialization failed when already active
* Incorrect file name caused deployment failure
* Delay in WordPress loading after deployment
* Confusion between containers and services in Swarm
* Multiple runs required cleanup using docker swarm leave --force

---

## Analysis (Compose vs Swarm)

| Feature        | Docker Compose | Docker Swarm       |
| -------------- | -------------- | ------------------ |
| Scope          | Single machine | Multi-node cluster |
| Scaling        | Manual         | Automatic          |
| Load Balancing | Not available  | Available          |
| Self-Healing   | Not available  | Available          |
| Use Case       | Development    | Production         |

---

## Result

Docker Swarm was successfully initialized.
A multi-container application was deployed as a stack, scaled, and tested for self-healing.

---

## Conclusion

Docker Swarm provides orchestration features such as scaling, load balancing, and self-healing, making it more suitable than Docker Compose for production-level deployments.

---

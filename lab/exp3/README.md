# Experiment 3 – Deploying NGINX Using Different Base Images and Comparing Image Layers

This lab demonstrates how to deploy NGINX containers using different base images and compare their image size, layers, performance, and use cases.

The experiment is performed using Docker containers on the local machine.

Built and tested using Docker from Docker Inc.

---

## Lab Objectives

After completing this lab, students will be able to:

- Deploy NGINX using the official image
- Build custom images using Ubuntu base
- Build custom images using Alpine base
- Understand Docker image layers
- Compare image size and performance
- Choose the correct base image for production

---

## Prerequisites

- Docker installed and running
- Basic knowledge of:
  - docker run
  - Dockerfile
  - Port mapping
  - Linux commands

Check Docker:

```
docker --version
```

---

# Part 1 – Deploy NGINX Using Official Image (Recommended)

## Pull image

```
docker pull nginx:latest
```

## Run container

```
docker run -d --name nginx-official -p 8080:80 nginx
```
![Screenshot 1](Screenshot%202026-02-10%20011515.png)

## Verify

Open browser:

```
http://localhost:8080
```
![Screenshot 2](Screenshot%202026-02-10%20011604.png)

You should see the NGINX welcome page.

---

## Observations

```
docker images nginx
```
![Screenshot 3](Screenshot%202026-02-10%20011632.png)

- Pre-optimized image
- Minimal setup required
- Production ready
- Debian-based internally

---

# Part 2 – Custom NGINX Using Ubuntu Base Image

## Dockerfile

```
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```
![Screenshot 4](Screenshot%202026-02-10%20011720.png)

## Build image

```
docker build -t nginx-ubuntu .
```

## Run container

```
docker run -d --name nginx-ubuntu -p 8081:80 nginx-ubuntu
```
![Screenshot 5](Screenshot%202026-02-10%20011935.png)
---

## Observations

```
docker images nginx-ubuntu
```

- Larger image
- More layers
- Full Linux tools available
- Slower startup

---

# Part 3 – Custom NGINX Using Alpine Base Image

## Dockerfile

```
FROM alpine:latest

RUN apk add --no-cache nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```
![Screenshot 6](Screenshot%202026-02-10%20012053.png)

## Build image

```
docker build -t nginx-alpine .
```
![Screenshot 7](Screenshot%202026-02-10%20012135.png)

## Run container

```
docker run -d --name nginx-alpine -p 8082:80 nginx-alpine
```
![Screenshot 8](Screenshot%202026-02-10%20012211.png)
---

## Observations

```
docker images nginx-alpine
```

- Extremely small size
- Minimal packages
- Faster pull
- Faster startup
- Better for microservices

---

# Part 4 – Image Size and Layer Comparison

## Compare sizes

```
docker images
```
![Screenshot 9](Screenshot%202026-02-10%20133122.png)

Typical result:

| Image | Approx Size |
|--------|-------------|
| nginx:latest | ~140 MB |
| nginx-ubuntu | ~220+ MB |
| nginx-alpine | ~25–30 MB |

---

## Inspect layers

```
docker history nginx
docker history nginx-ubuntu
docker history nginx-alpine
```
![Screenshot 10](Screenshot%202026-02-10%20012324.png)

Observations:

- Ubuntu → many layers
- Alpine → minimal layers
- Official → optimized

---

# Part 5 – Functional Tasks

## Serve custom HTML

```
mkdir html
echo "<h1>Hello from Docker NGINX</h1>" > html/index.html
```

Run:

```
docker run -d -p 8083:80 -v $(pwd)/html:/usr/share/nginx/html nginx
```

Open:

```
http://localhost:8083
```

---

## Reverse Proxy (Conceptual)

NGINX can:

- Forward traffic to backend services
- Load balance multiple containers
- Terminate SSL

Used for:

- API gateway
- Microservices frontend
- Static hosting

---

# Part 6 – Comparison Summary

| Feature | Official | Ubuntu | Alpine |
|-----------|------------|-----------|-----------|
| Size | Medium | Large | Very Small |
| Startup | Fast | Slow | Very Fast |
| Tools | Limited | Many | Minimal |
| Security | Medium | Large surface | Small surface |
| Production | Yes | Rarely | Yes |

---

# Part 7 – When to Use What

Use Official image when:
- Production deployment
- Standard hosting
- Reverse proxy

Use Ubuntu image when:
- Learning Linux
- Debugging needed
- System tools required

Use Alpine image when:
- Microservices
- CI/CD
- Cloud workloads
- Small image required

---

# Commands Summary

Pull:

```
docker pull nginx
```

Run:

```
docker run -d -p 8080:80 nginx
```

Build custom:

```
docker build -t nginx-ubuntu .
docker build -t nginx-alpine .
```

Inspect:

```
docker images
docker history image_name
```

Stop:

```
docker stop container_name
docker rm container_name
```

---

# Learning Outcomes

After this experiment you should understand:

- Docker image construction
- Base image impact on size
- Image layering
- Performance trade-offs
- Real-world use of NGINX in containers

---

# Key Takeaway

Smaller base images reduce:

- size
- attack surface
- startup time

Alpine is best for lightweight production, while official images are safest for standard deployments.

---

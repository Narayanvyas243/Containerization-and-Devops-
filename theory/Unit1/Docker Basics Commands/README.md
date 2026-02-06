# 🐳 Docker Basic Commands & Flags – Hands On

This unit explains all **basic Docker commands** required for image management, containers, networking, volumes, monitoring, and builds.

These commands are essential for **Containerization & DevOps labs + exams**.

---

# 📌 1. Docker Basics

## Check Docker Version
```bash
docker version
```
Shows:
- Client version
- Server version
- Confirms Docker daemon connectivity

## System Information
```bash
docker info
```
Displays:
- Storage driver
- Images
- Containers
- CPU / Memory info

---

# 📌 2. Image Management

## List Local Images
```bash
docker images
```

Flags:
- `-a` → show all images
- `-q` → only image IDs


---

## Pull Image from Registry
```bash
docker pull ubuntu
docker pull ubuntu:22.04
```
Downloads images from Docker Hub.

---

## Remove Image
```bash
docker rmi ubuntu
```

Flags:
- `-f` → force remove
- `-a` → remove unused images

---

# 📌 3. Container Lifecycle

## Run Container
```bash
docker run ubuntu
```

Common flags:
- `-it` → interactive terminal
- `-d` → detached mode
- `--name` → custom name
- `--rm` → auto remove

Example:
```bash
docker run -it --name test ubuntu bash
```

---

## List Containers
```bash
docker ps
```

Flags:
- `-a` → all containers
- `-q` → only IDs


---

## Start / Stop / Restart
```bash
docker start container_name
docker stop container_name
docker restart container_name
```

---

## Remove Container
```bash
docker rm container_name
```


---

# 📌 4. Execute Commands Inside Containers

## Attach Terminal
```bash
docker attach container_name
```

## Execute Command
```bash
docker exec -it container_name bash
```

Flags:
- `-i` → interactive
- `-t` → terminal


---

# 📌 5. Networking & Ports

## Port Mapping
```bash
docker run -d -p 8080:80 nginx
```

Explanation:
- Host port → 8080
- Container port → 80

Flags:
- `-p host:container`
- `-P` → random ports

---

## List Networks
```bash
docker network ls
```

## Create Network
```bash
docker network create mynet
```


---

# 📌 6. Volumes & Data Persistence

## Create Volume
```bash
docker volume create mydata
```

## Mount Volume
```bash
docker run -v mydata:/data ubuntu
```

Bind mount:
```bash
docker run -v /host/path:/container/path ubuntu
```

Read only:
```bash
docker run -v mydata:/data:ro ubuntu
```


---

# 📌 7. Logs & Monitoring

## View Logs
```bash
docker logs container_name
```

Flags:
- `-f` → follow live
- `--tail 50`
- `--since 10m`

---

## Resource Usage
```bash
docker stats
```

Shows:
- CPU
- Memory
- Network I/O
- Disk I/O


---

# 📌 8. Inspect & Metadata

## Inspect Object
```bash
docker inspect container_name
```

Returns:
- IP address
- Mounts
- Environment variables
- Network configuration (JSON)

---

# 📌 9. Docker Build (Images)

## Build Image
```bash
docker build -t myapp .
```

Flags:
- `-t` → tag
- `-f` → custom Dockerfile
- `--no-cache`

---

## Example Dockerfile
```dockerfile
FROM ubuntu:22.04
RUN apt update && apt install -y nginx
CMD ["nginx", "-g", "daemon off;"]
```

---

# 📌 10. Cleanup Commands

## Remove unused containers
```bash
docker container prune
```

## Remove unused images
```bash
docker image prune
```

## Remove everything unused
```bash
docker system prune -a --volumes
```

---

# 📌 11. Docker Compose (Overview)

Start services:
```bash
docker compose up -d
```

Stop services:
```bash
docker compose down
```

---

# 📌 12. Important Run Flags (Quick Reference)

| Flag | Meaning |
|--------|------------|
| -it | Interactive terminal |
| -d | Detached mode |
| --rm | Auto remove |
| --name | Container name |
| -p | Port mapping |
| -v | Volume mount |
| -e | Environment variable |
| --network | Custom network |
| --restart=always | Auto restart |

---

# 📌 13. Minimal Lab Example (Ubuntu + Nginx)

```bash
docker pull nginx
docker run -d --name web -p 8080:80 nginx
docker ps
docker logs web
docker stop web
docker rm web
```

---

# 📌 14. Key Concepts Summary

| Term | Meaning |
|-----------|-----------------------|
| Image | Blueprint/template |
| Container | Running instance |
| Volume | Persistent storage |
| Network | Container communication |
| Dockerfile | Image build instructions |

---

# ✅ Conclusion

This lab covered:
✔ Image management  
✔ Container lifecycle  
✔ Networking  
✔ Volumes  
✔ Logs & monitoring  
✔ Build process  
✔ Cleanup  

These are the **core Docker commands every DevOps engineer must know**.


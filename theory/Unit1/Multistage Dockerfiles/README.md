# Experiment – Multistage Dockerfiles: Explanation & Benefits

This experiment demonstrates how Multistage Dockerfiles reduce image size, improve build speed, and enhance security by separating the build environment from the runtime environment.

Using multiple `FROM` statements, we build applications in one stage and copy only the required artifacts into a minimal production image.

---

## Objectives

After completing this experiment, you will be able to:

- Understand multistage Docker builds
- Reduce Docker image size
- Separate build and runtime environments
- Improve container security
- Compare single-stage vs multistage images
- Use scratch for minimal runtime images

---

## Prerequisites

- Docker installed and running
- Basic knowledge of:
  - docker build
  - docker run
  - Dockerfile
  - Linux commands

Check installation:

```
docker --version
```

---

# What is a Multistage Dockerfile?

A Multistage Dockerfile uses multiple `FROM` instructions.

Each stage:

- can have its own base image
- installs its own dependencies
- performs build tasks
- passes only required files to the next stage

Final image contains only runtime components.

---

# Why Use Multistage Builds?

## Key Benefits

### 1. Reduced Image Size
- No build tools in final image
- Removes intermediate files
- Removes source code
- Smaller downloads

### 2. Improved Build Speed
- Layer caching
- Faster push/pull
- Parallel builds

### 3. Enhanced Security
- Minimal attack surface
- No compilers or package managers
- Production-only runtime

### 4. Clean Separation of Concerns
- Build stage → development tools
- Runtime stage → production only

---

# scratch Image

`scratch` is an empty base image.

It contains:

- No shell
- No OS
- No libraries
- 0 bytes size

Used for:

- statically compiled binaries (C/Go)

---

# C Program Example

## hello.c

```
#include <stdio.h>

int main() {
    printf("Hello from Multistage Docker!\n");
    return 0;
}
```

---

## Single-Stage Build (Problematic)

### Dockerfile.single

```
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y gcc

COPY hello.c .

RUN gcc -static -o hello hello.c

CMD ["./hello"]
```

### Build

```
docker build -f Dockerfile.single -t c-app:single .
```
![Step 1](Screenshot%202026-02-10%20153738.png)
![Step 2](Screenshot%202026-02-10%20174756.png)
### Problem

- Includes Ubuntu
- Includes gcc
- Includes build tools
- Very large image (~150MB+)

---

## Multistage Build (Optimized)

### Dockerfile.multi

```
# Stage 1: Build
FROM ubuntu:22.04 AS builder

RUN apt-get update && apt-get install -y gcc

COPY hello.c .

RUN gcc -static -o hello hello.c


# Stage 2: Runtime
FROM scratch

COPY --from=builder /hello /hello

CMD ["/hello"]
```
![Step 4](Screenshot%202026-02-10%20175041.png)
![Step 5](Screenshot%202026-02-10%20175105.png)
### Build

```
docker build -f Dockerfile.multi -t c-app:multi .
```

### Result

Image size reduces from:

150MB → ~1MB

---

# Compare Image Sizes

```
docker images
```
![Step 3](Screenshot%202026-02-10%20175029.png)

Typical output:

| Image | Size |
|-------|--------|
| c-app:single | ~150MB |
| c-app:multi | ~1MB |

---

# Inspect Layers

```
docker history c-app:single
docker history c-app:multi
```

Observations:

Single-stage:
- many layers
- OS + tools included

Multistage:
- very few layers
- only binary

---

# Java Program Example
![Step 6](Screenshot%202026-02-11%20010120.png)
## Single-stage (Problem)

```
FROM maven:3.8-openjdk-11
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package
CMD ["java","-jar","target/app.jar"]
```
![Step 10](Screenshot%202026-02-11%20010717.png)

Issues:
- Maven included
- JDK included
- Source code exposed
- Large image (~750MB)

---

## Multistage (Optimized)

```
# Stage 1: Build
FROM maven:3.8-openjdk-11 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM openjdk:11-jre-slim
WORKDIR /app
COPY --from=builder /app/target/app.jar app.jar

RUN useradd -m myuser
USER myuser

CMD ["java","-jar","app.jar"]
```
![Step 9](Screenshot%202026-02-11%20010706.png)
![Step 11](Screenshot%202026-02-11%20010729.png)
![Step 12](Screenshot%202026-02-11%20010745.png)
Benefits:

- Smaller image
- No source code
- No Maven
- Secure non-root execution

---

# Performance Comparison

| Aspect | Single Stage | Multistage |
|------------|---------------|---------------|
| Image Size | 500MB–1GB | 50–200MB |
| Build Time | Slower | Faster |
| Security | High risk | Minimal |
| Push/Pull | Slow | Fast |
| Deployment | Heavy | Lightweight |

---

# When to Use Multistage

Use when:

- Production deployments
- Microservices
- CI/CD pipelines
- Security-sensitive apps
- Cloud environments

---

# When NOT to Use

Avoid when:

- Development containers
- Debugging environments
- Simple scripts
- Quick prototypes

---

# Commands Summary

Build single-stage:

```
docker build -f Dockerfile.single -t c-app:single .
```
![Step 7](Screenshot%202026-02-11%20010531.png)
Build multistage:

```
docker build -f Dockerfile.multi -t c-app:multi .
```
![Step 13](Screenshot%202026-02-11%20011441.png)

Run:

```
docker run c-app:single
docker run c-app:multi
```
![Step 8](Screenshot%202026-02-11%20010608.png)
![Step 14](Screenshot%202026-02-11%20011503.png)

Inspect:

```
docker images
docker history image_name
```
![Step 15](Screenshot%202026-02-11%20011611.png)
---

# Learning Outcomes

After this lab, you understand:

- How multistage builds work
- Why images become smaller
- Why scratch is used
- Security improvements
- Real-world production practices

---

# Key Takeaway

Multistage Dockerfiles create images that are:

- Smaller
- Faster
- Secure
- Production ready

Always use multistage builds for production containers.

---

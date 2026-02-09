# Dockerfile – Hands-On Tutorial (Automation-First Approach)

This project demonstrates how to build Docker images using a Dockerfile instead of manually modifying containers.  
The focus is automation, reproducibility, CI/CD compatibility, and production-ready practices.

---

## Why docker commit is NOT suitable for automation

Using `docker commit` after manually changing a running container causes multiple problems:

- Not reproducible
- Not version controlled
- Not deterministic
- Not CI/CD friendly
- Hard to maintain

Conclusion:

If it cannot be rebuilt automatically, it does not scale.  
Always use a Dockerfile.

---

## What is a Dockerfile

A Dockerfile is a text file containing step-by-step instructions to build a Docker image.

Key properties:

- Declarative
- Reproducible
- Version controllable
- Automatable
- Deterministic

---

# Dockerfile Core Instructions

## FROM
Specifies the base image.

```
FROM ubuntu:22.04
```

Must be the first instruction.

---

## RUN
Executes commands at build time.

```
RUN apt update && apt install -y openjdk-17-jdk
```

Used for installing packages and configuring the system.

---

## WORKDIR
Sets working directory inside the image.

```
WORKDIR /home/app
```

Automatically creates the directory and replaces `cd`.

---

## COPY
Copies files from host to image.

```
COPY Hello.java .
```

Preferred over ADD.

---

## ADD
Similar to COPY but with extra features.

```
ADD app.tar /home/app
```

Rarely used.

---

## ENV
Sets environment variables.

```
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
```

---

## EXPOSE
Documents container listening port.

```
EXPOSE 8080
```

Does NOT publish the port automatically.

---

## CMD
Default command when container starts.

```
CMD ["java", "Hello"]
```

Can be overridden at runtime.

---

## ENTRYPOINT
Defines the fixed executable.

```
ENTRYPOINT ["java"]
```

Often combined with CMD.

---

# Hands-On: Build Java App Using Dockerfile

## Project Structure

```
java-docker/
├── Dockerfile
└── Hello.java
```

---

## Hello.java

```
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello from Dockerfile automation");
    }
}
```

---

## Dockerfile

```
FROM ubuntu:22.04

RUN apt update && apt install -y openjdk-17-jdk

WORKDIR /home/app

COPY Hello.java .

RUN javac Hello.java

CMD ["java", "Hello"]
```

---

# Build Image

From the project directory:

```
docker build -t java-app:1.0 .
```

Explanation:

- `-t` → image name and tag
- `.` → build context

Check images:

```
docker images
```

---

# Run Container

```
docker run java-app:1.0
```

Output:

```
Hello from Dockerfile automation
```

---

# Override CMD at Runtime

```
docker run -it java-app:1.0 bash
```

CMD gets replaced by `bash`.

---

# Layered Build Concept

Each Dockerfile instruction creates a layer:

```
FROM ubuntu
RUN install java
WORKDIR /home/app
COPY Hello.java
RUN javac Hello.java
CMD java Hello
```

Benefits:

- Faster rebuilds
- Layer caching
- Smaller downloads
- Better debugging

---

# Rebuild After Code Change

After modifying Hello.java:

```
docker build -t java-app:1.1 .
```

Only layers after COPY are rebuilt.

---

# Share Dockerfile-Based Image

Tag:

```
docker tag java-app:1.0 username/java-app:1.0
```

Push:

```
docker push username/java-app:1.0
```

Pull anywhere:

```
docker pull username/java-app:1.0
docker run username/java-app:1.0
```

---

# Dockerfile vs docker commit

| Aspect | Dockerfile | docker commit |
|--------|------------|---------------|
| Automation | Yes | No |
| Version control | Yes | No |
| CI/CD | Yes | No |
| Reproducible | Yes | No |
| Production ready | Yes | No |

---

# CMD and ENTRYPOINT in Docker

## Purpose at a glance

| Aspect | CMD | ENTRYPOINT |
|-----------|-----------|-------------|
| Main role | Default command/arguments | Main executable |
| Override | Easy | Difficult |
| Flexibility | High | Controlled |

---

# CMD Example

```
FROM ubuntu
CMD ["echo", "Hello World"]
```

Run:

```
docker run myimage
```

Override:

```
docker run myimage echo "Hi"
```

CMD is replaced.

---

# ENTRYPOINT Example

```
FROM ubuntu
ENTRYPOINT ["echo"]
```

Run:

```
docker run myimage Hello
```

Arguments are appended, not replaced.

---

# ENTRYPOINT + CMD Together (Best Practice)

```
FROM ubuntu
ENTRYPOINT ["echo"]
CMD ["Hello World"]
```

Behavior:

```
docker run myimage
→ Hello World

docker run myimage Hi
→ Hi
```

Internal working:

ENTRYPOINT + CMD → final command

---

# Overriding Behavior Summary

| Scenario | CMD | ENTRYPOINT |
|-----------|-----------|--------------|
| docker run image | Used | Used |
| docker run image ls | Replaced | Appended |
| docker run --entrypoint bash image | Ignored | Replaced |

---

# Exec Form vs Shell Form

Recommended:

```
CMD ["nginx", "-g", "daemon off;"]
```

Not recommended:

```
CMD nginx -g "daemon off;"
```

Exec form provides proper signal handling.

---

# When to Use What

Use CMD when:

- You want default behavior
- Users may override easily

Use ENTRYPOINT when:

- Container behaves like a binary
- Execution must be controlled

Use both when:

- Fixed executable with configurable arguments

---

# Best Practice for Java Containers

```
CMD ["java", "Hello"]
```

or

```
ENTRYPOINT ["java", "Hello"]
```

Avoid shells and multiple processes.

---

# Quick Rule to Remember

ENTRYPOINT = What runs  
CMD = With what arguments  

---

# Key Takeaway

Dockerfile enables Infrastructure as Code.  
Always prefer automated, reproducible builds over manual container changes.


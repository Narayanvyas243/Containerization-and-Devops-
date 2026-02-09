# Preserving Changes Inside a Docker Container

This unit explains how to **save modifications made inside a running container** and reuse them later.

You will learn:

- How to modify a container
- How to convert container → image
- How to reuse the image
- How to transfer images offline
- Difference between export vs save

---

# Scenario Overview

Goal:

✔ Start Ubuntu container  
✔ Install Java (javac)  
✔ Create Java app  
✔ Save changes  
✔ Reuse and share image  

---

#  Step 1 – Run Base Ubuntu Container

```bash
docker run -it --name java_lab ubuntu:22.04 bash
```
## 📸 Screenshots

![Screenshot 1](Screenshot%202026-02-09%20110202.png)


Explanation:
- `-it` → interactive terminal
- `--name java_lab` → container name
- `bash` → open shell

Now you are **inside the container**.

---

#  Step 2 – Install Java Compiler (Inside Container)

```bash
apt update
apt install -y openjdk-17-jdk
```

Verify:

```bash
javac --version
```
![Screenshot 2](Screenshot%202026-02-09%20110209.png)

Now Java is installed inside container.

---

#  Step 3 – Create Java App in /home/app

```bash
mkdir -p /home/app
cd /home/app
```

Create file:

```bash
nano Hello.java
```

Paste:

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello from Docker container!");
    }
}
```

Compile:

```bash
javac Hello.java
```

Run:

```bash
java Hello
```

Output:
```
Hello from Docker container!
```
![Screenshot 3](Screenshot%202026-02-09%20110345.png)

Container now contains:
✔ Java installed  
✔ Java source file  
✔ Compiled class  

---

# 🚀 Step 4 – Exit Container

```bash
exit
```

Container stops.

⚠️ Important:
Changes are **not lost**, they remain inside the stopped container.

---

#  Step 5 – Convert Container → Image (docker commit)

```bash
docker commit java_lab myrepo/java-app:1.0
```

What happens:
- Snapshot of container filesystem
- New reusable image created

Verify:

```bash
docker images
```
![Screenshot 4](Screenshot%202026-02-09%20110319.png)

---

#  Step 6 – Reuse the Image

Run new container:

```bash
docker run -it myrepo/java-app:1.0 bash
```

Test:

```bash
cd /home/app
java Hello
```

Java and program already exist 

---

#  Step 7 – Save / Load (Offline Transfer)

## Save image
```bash
docker save -o java-app.tar myrepo/java-app:1.0
```

Creates:
```
java-app.tar
```

Transfer this file using USB/SCP.

---

## Load image
```bash
docker load -i java-app.tar
```

Check:

```bash
docker images
```

Image restored successfully.

---

# 📌 Export vs Save (Important Difference)

## docker export (Not recommended normally)

```bash
docker export java_lab > container.tar
```

Exports:
✔ filesystem only

Loses:
❌ image name
❌ layers
❌ CMD
❌ metadata

---

## docker save (Recommended)

```bash
docker save -o image.tar myrepo/java-app:1.0
```

Preserves:
✔ layers
✔ metadata
✔ tags
✔ CMD/ENTRYPOINT

---

# 📌 Command Summary Table

| Command | Purpose |
|----------|----------------------------|
| docker commit | Container → Image |
| docker save | Image → File |
| docker load | File → Image |
| docker push/pull | Share via registry |
| docker export/import | Raw filesystem only |

---

#  Best Practice (Production Method)

Instead of `docker commit`, use **Dockerfile**.

Example:

```dockerfile
FROM ubuntu:22.04
RUN apt update && apt install -y openjdk-17-jdk
WORKDIR /home/app
COPY Hello.java .
RUN javac Hello.java
CMD ["java", "Hello"]
```

Build:

```bash
docker build -t java-app:2.0 .
```

👉 Dockerfile is preferred for real projects  
👉 docker commit is fine for labs & learning

---

# 🎯 One-Line Summary

Modified container → docker commit  
Reuse locally → docker run  
Share online → docker push/pull  
Share offline → docker save/load  

---




# Experiment 1 – Compare Virtual Machine (Vagrant) with Container (Docker)


**Name:** NARAYAN VYAS  
**SAP ID:** 500122747  
**Batch:** 2(CCVT)

---

## 🎯 Aim
To compare Virtual Machines (VMs) using **Vagrant** and Containers using **Docker**, and analyze differences in performance, resource usage, startup time, and efficiency.

---

## 🧠 Theory

### Virtual Machine (VM)
A Virtual Machine runs a complete Guest Operating System on top of a hypervisor.  
Each VM contains:
- Full OS
- Libraries
- Dependencies
- Application

VMs are heavy and require more memory and storage.

---

### Container
A container shares the Host OS kernel and runs only the application with its dependencies.  
Containers are lightweight and start very quickly.

Docker is used to create and manage containers.

---

## ⚙ Tools Used
- Vagrant
- VirtualBox
- Docker Desktop
- Windows OS

---

# 🧪 Procedure

---

## Part A – Virtual Machine using Vagrant

### Step 1 – Initialize Vagrant project
---
```bash
vagrant init ubuntu/bionic64
````
### Step 2 – Start VM
---
```bash
vagrant up
```
### Step 3 – Connect to VM
---
```bash
vagrant ssh
```
### Step 4 – Check OS inside VM
---
```bash
ls
uname -a
```
### Step 5 – Stop VM
---
```bash
vagrant halt
```
### Step 6 – Destroy VM
---
```bash
vagrant destroy
```
---
### Observations (VM)

- Takes more time to boot

- Uses more RAM and disk space

- Separate full OS required
  ---
 ## Part B – Container using Docker
 ---
 ### Step 1 – Check Docker installation
 ```bash
docker --version
```
---
### Step 2 – Run test container
```bash
docker run hello-world
```
---
### Step 3 – Pull Ubuntu image
```bash
docker pull ubuntu
```
---
### Step 4 – Run container interactively
```bash
docker run -it ubuntu bash
```
---
### Step 5 – List running containers
```bash
docker ps
```
---
### Step 6 – Stop container
```bash
docker stop <container_id>
```
---
### Observations (Container)

Starts within seconds

Lightweight

Uses less memory

Shares host OS

Faster performance




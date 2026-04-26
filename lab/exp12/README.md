# Experiment 12: Study and Analyse Container Orchestration using Kubernetes

## Objective

To understand Kubernetes concepts and perform deployment, service exposure, scaling, and self-healing using a WordPress application.

---

## Tools and Technologies Used

* Kubernetes (Minikube)
* kubectl
* Docker
* WordPress container image

---

## System Setup

### Step 1: Install Dependencies

```bash
sudo apt update
sudo apt install -y curl apt-transport-https docker.io
```
![ss](./Screenshot%202026-04-24%20234104.png)
### Step 2: Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
![ss](./Screenshot%202026-04-24%20235410.png)
### Step 3: Verify Installation

```bash
minikube version
kubectl version --client
docker --version
```
![ss](./Screenshot%202026-04-24%20234123.png)

---

## Starting Kubernetes Cluster

```bash
minikube start --driver=docker
```
![ss](./Screenshot%202026-04-24%20235419.png)
Check cluster:

```bash
kubectl get nodes
```

---

## Task 1: Create WordPress Deployment

### Create file: wordpress-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
      - name: wordpress
        image: wordpress:latest
        ports:
        - containerPort: 80
```
![ss](./Screenshot%202026-04-24%20235445.png)
### Apply Deployment

```bash
kubectl apply -f wordpress-deployment.yaml
```
![ss](./Screenshot%202026-04-24%20235510.png)
### Verify Pods

```bash
kubectl get pods
```

---

## Task 2: Expose Deployment as Service

### Create file: wordpress-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: wordpress-service
spec:
  type: NodePort
  selector:
    app: wordpress
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```
![ss](./Screenshot%202026-04-24%20235526.png)
### Apply Service

```bash
kubectl apply -f wordpress-service.yaml
```
![ss](./Screenshot%202026-04-24%20235606.png)
### Verify Service

```bash
kubectl get svc
```
![ss](./Screenshot%202026-04-24%20235733.png)

---

## Task 3: Access Application

Attempted URL:

```text
http://192.168.49.2:30007
```
![ss](./Screenshot%202026-04-25%20000409.png)
This did not work due to WSL and Docker driver networking limitations.

### Correct Method Used:

```bash
minikube service wordpress-service
```
![ss](./Screenshot%202026-04-25%20000658.png)
Output:

```text
http://127.0.0.1:42465
```

Accessed application using:

```text
http://127.0.0.1:42465
```
![ss](./Screenshot%202026-04-25%20000616.png)

---

## Problem Faced 1: Minikube Command Not Found

Error:

```text
minikube: command not found
```

### Solution:

Installed Minikube manually using curl and moved binary to `/usr/local/bin`.

---

## Problem Faced 2: Service Not Opening via Minikube IP

Issue:

* URL `192.168.49.2:30007` was not accessible

Reason:

* Running Minikube inside WSL with Docker driver
* NodePort not directly accessible from Windows host

### Solution:

Used:

```bash
minikube service wordpress-service
```

---

## Problem Faced 3: Error Establishing Database Connection

Error:

```text
Error establishing a database connection
```

### Reason:

* WordPress requires MySQL database
* Database was not configured in deployment

### Solution:

#### Create MySQL Pod

```bash
nano mysql.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysql
  labels:
    app: wordpress
spec:
  containers:
  - name: mysql
    image: mysql:5.7
    env:
    - name: MYSQL_ROOT_PASSWORD
      value: root
    - name: MYSQL_DATABASE
      value: wordpress
    ports:
    - containerPort: 3306
```

Apply:

```bash
kubectl apply -f mysql.yaml
```

---

#### Connect WordPress to MySQL

Edit deployment:

```bash
kubectl edit deployment wordpress
```

Add:

```yaml
env:
- name: WORDPRESS_DB_HOST
  value: mysql
- name: WORDPRESS_DB_PASSWORD
  value: root
```

Restart deployment:

```bash
kubectl rollout restart deployment wordpress
```

---

## Task 4: Scaling the Deployment

Increase replicas:

```bash
kubectl scale deployment wordpress --replicas=4
```

Verify:

```bash
kubectl get pods
```

Result:

* Number of pods increased from 2 to 4

---

## Task 5: Self-Healing Demonstration

Get pod list:

```bash
kubectl get pods
```

Delete one pod:

```bash
kubectl delete pod <pod-name>
```

Check again:

```bash
kubectl get pods
```

Result:

* Deleted pod was automatically recreated
* Total pods remained 4

---

## Kubernetes Concepts Learned

* Pod: Smallest unit in Kubernetes
* Deployment: Manages pod lifecycle and replicas
* Service: Exposes application externally
* Scaling: Increasing number of pods
* Self-healing: Automatic pod recovery

---

## Commands Summary

```bash
kubectl apply -f file.yaml
kubectl get pods
kubectl get svc
kubectl scale deployment wordpress --replicas=4
kubectl delete pod <pod-name>
kubectl rollout restart deployment wordpress
minikube start --driver=docker
minikube service wordpress-service
```

---

## Conclusion

* Successfully deployed WordPress using Kubernetes
* Exposed application using NodePort service
* Accessed application via Minikube service tunnel
* Performed scaling from 2 to 4 replicas
* Demonstrated self-healing by deleting a pod
* Identified and resolved networking and database issues

This experiment provided practical understanding of Kubernetes orchestration features and real-world deployment challenges.

---


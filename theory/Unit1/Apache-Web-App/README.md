#  Kubernetes Hands-on Lab: Apache Web App (httpd)

##  Objective

Deploy and manage a simple Apache-based web server using Kubernetes.
In this lab, you will:

* Run a Pod
* Inspect it
* Access the web app
* Convert to Deployment
* Scale the application
* Debug errors
* Explore container internals
* Observe self-healing

---

#  Task 1: Run Apache Pod

## 🔹 Step 1: Run Pod

```bash
kubectl run apache-pod --image=httpd
```
![Screenshot](./Screenshot%202026-03-25%20123736.png)

Check:

```bash
kubectl get pods
```

---

## 🔹 Step 2: Inspect Pod

```bash
kubectl describe pod apache-pod
```
![Screenshot](./Screenshot%202026-03-25%20123749.png)
![Screenshot](./Screenshot%202026-03-25%20123809.png)

Focus:

* Image: httpd
* Port: 80
* Events

---
![Screenshot](./Screenshot%202026-03-25%20123828.png)

![Screenshot](./Screenshot%202026-03-25%20123840.png)

## 🔹 Step 3: Access the App

```bash
kubectl port-forward pod/apache-pod 8081:80
```
![Screenshot](./Screenshot%202026-03-25%20123905.png)

Open in browser:

```
http://localhost:8081
```

![Screenshot](./Screenshot%202026-03-25%20123912.png)

 Output: *Apache default page ("It works!")*

---

## 🔹 Step 4: Delete Pod

```bash
kubectl delete pod apache-pod
```

 Pod is deleted permanently (no self-healing)

---

#  Task 2: Convert to Deployment

## 🔹 Step 5: Create Deployment

```bash
kubectl create deployment apache --image=httpd
```
![Screenshot](./Screenshot%202026-03-25%20124044.png)

Check:

```bash
kubectl get deployments
kubectl get pods
```

---

## 🔹 Step 6: Expose Deployment

```bash
kubectl expose deployment apache --port=80 --type=NodePort
```
![Screenshot](./Screenshot%202026-03-25%20124217.png)

Port forward:

```bash
kubectl port-forward service/apache 8082:80
```

Open:

```
http://localhost:8082
```

![Screenshot](./Screenshot%202026-03-25%20124229.png)

---

#  Task 3: Scaling

## 🔹 Step 7: Scale Deployment

```bash
kubectl scale deployment apache --replicas=2
```
![Screenshot](./Screenshot%202026-03-25%20124324.png)

Check:

```bash
kubectl get pods
```

 Multiple pods running

---


## 🔹 Step 8: Test Load Distribution

Refresh browser multiple times:

```
http://localhost:8082
```

---

#  Task 4: Debugging Scenario

## 🔹 Step 9: Break the App

```bash
kubectl set image deployment/apache httpd=wrongimage
```
![Screenshot](./Screenshot%202026-03-25%20124433.png)

Check:

```bash
kubectl get pods
```


 Status: ImagePullBackOff

---

## 🔹 Step 10: Diagnose

```bash
kubectl describe pod <pod-name>
```
![Screenshot](./Screenshot%202026-03-25%20125602.png)
![Screenshot](./Screenshot%202026-03-25%20125613.png)

Look for:

* ImagePullBackOff
* Error messages

---

## 🔹 Step 11: Fix It

```bash
kubectl set image deployment/apache httpd=httpd
```
![Screenshot](./Screenshot%202026-03-25%20125652.png)

Verify:

```bash
kubectl get pods
```
![Screenshot](./Screenshot%202026-03-25%20130113.png)

---

#  Task 5: Explore Inside Container

## 🔹 Step 12: Exec into Pod

```bash
kubectl exec -it <pod-name> -- /bin/bash
```
![Screenshot](./Screenshot%202026-03-25%20130113.png)
Inside container:

```bash
ls /usr/local/apache2/htdocs
```

 Web files location

Exit:

```bash
exit
```

---

#  Task 6: Self-Healing

## 🔹 Step 13: Delete One Pod

```bash
kubectl delete pod <pod-name>
```
![Screenshot](./Screenshot%202026-03-25%20130248.png)

Watch:

```bash
kubectl get pods -w
```

 Kubernetes recreates pod automatically

---

#  Task 7: Cleanup

```bash
kubectl delete deployment apache
kubectl delete service apache
```

![Screenshot](./Screenshot%202026-03-25%20130433.png)

---

#  Optional Challenge

Modify website content:

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

```bash
echo "Hello from Kubernetes" > /usr/local/apache2/htdocs/index.html
```

Refresh browser to see changes.

---

#  Common Errors & Fixes

##  Pod not running

```bash
kubectl get pods
```

---

##  Bash not found

```bash
kubectl exec -it <pod-name> -- /bin/sh
```

---

##  Image issue persists

```bash
kubectl rollout restart deployment apache
```

---

#  What You Learned

* Difference between Pod and Deployment
* Scaling applications
* Debugging container issues
* Port forwarding
* Accessing container filesystem
* Kubernetes self-healing

---

#  Conclusion

This lab demonstrates core Kubernetes concepts through a real-world Apache web application. It provides hands-on experience with deployment, scaling, debugging, and container management.

---


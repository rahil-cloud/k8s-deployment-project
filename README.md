# Kubernetes Deployment Project using Docker and K3s

## Overview

This project demonstrates how to:

- Install Docker
- Install K3s Kubernetes
- Create a simple web application
- Build a Docker image
- Deploy the application to Kubernetes
- Expose the application using a NodePort Service

---

## Prerequisites

- Ubuntu Server / AWS EC2 Instance
- Internet Connection
- SSH Access
- Sudo Privileges

---

# Step 1: Update Server

```bash
sudo apt update -y
```

---

# Step 2: Install Docker

Install Docker:

```bash
sudo apt install docker.io -y
```

Start Docker:

```bash
sudo systemctl start docker
```

Enable Docker on Boot:

```bash
sudo systemctl enable docker
```

Verify Installation:

```bash
docker --version
```

Expected Output:

```bash
Docker version 29.x.x
```

---

# Step 3: Configure Docker Permissions

Add current user to Docker group:

```bash
sudo usermod -aG docker $USER
```

Apply group changes:

```bash
newgrp docker
```

Test Docker:

```bash
docker run hello-world
```

Expected Output:

```bash
Hello from Docker!
```

---

# Step 4: Install K3s Kubernetes

Install K3s:

```bash
curl -sfL https://get.k3s.io | sh -
```

Verify Node:

```bash
sudo kubectl get nodes
```

Expected Output:

```bash
NAME             STATUS   ROLES
ip-xxx-xxx-xx    Ready    control-plane
```

---

# Step 5: Configure Kubectl Access

Allow kubeconfig access:

```bash
sudo chmod 644 /etc/rancher/k3s/k3s.yaml
```

Create kube directory:

```bash
mkdir -p ~/.kube
```

Copy kubeconfig:

```bash
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
```

Change ownership:

```bash
sudo chown $USER:$USER ~/.kube/config
```

Set environment variable:

```bash
export KUBECONFIG=~/.kube/config
```

Make it permanent:

```bash
echo 'export KUBECONFIG=~/.kube/config' >> ~/.bashrc
source ~/.bashrc
```

Verify:

```bash
kubectl get nodes
```

---

# Step 6: Create Project Directory

```bash
mkdir k8s-deployment-project
cd k8s-deployment-project
```

---

# Step 7: Create HTML File

Create file:

```bash
nano index.html
```

Paste:

```html
<h1>Hello Kubernetes 🚀</h1>
```

Save and Exit.

---

# Step 8: Create Dockerfile

Create file:

```bash
nano Dockerfile
```

Paste:

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html
```

Save and Exit.

---

# Step 9: Build Docker Image

```bash
docker build -t my-web-app .
```

Verify:

```bash
docker images
```

Expected Output:

```bash
my-web-app
```

---

# Step 10: Create Kubernetes Deployment

Create file:

```bash
nano deployment.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: my-web-app

spec:
  replicas: 1

  selector:
    matchLabels:
      app: my-web-app

  template:
    metadata:
      labels:
        app: my-web-app

    spec:
      containers:
      - name: my-web-app
        image: my-web-app
        imagePullPolicy: Never

        ports:
        - containerPort: 80
```

Save and Exit.

---

# Step 11: Deploy Application

Apply Deployment:

```bash
kubectl apply -f deployment.yaml
```

Check Deployment:

```bash
kubectl get deployments
```

Check Pods:

```bash
kubectl get pods
```

---

# Step 12: Expose Application

Create NodePort Service:

```bash
kubectl expose deployment my-web-app --type=NodePort --port=80
```

Check Service:

```bash
kubectl get svc
```

Example Output:

```bash
NAME         TYPE       PORT(S)
my-web-app   NodePort   80:30206/TCP
```

---

# Step 13: Get EC2 Public IP

```bash
curl ifconfig.me
```

Example:

```bash
13.51.xx.xx
```

---

# Step 14: Configure AWS Security Group

Open EC2 Security Group.

Add Inbound Rule:

| Type | Port | Source |
|--------|--------|--------|
| Custom TCP | 30206 | 0.0.0.0/0 |

Save Changes.

---

# Step 15: Access Application

Open Browser:

```text
http://<EC2-PUBLIC-IP>:30206
```

Example:

```text
http://13.51.xx.xx:30206
```

Expected Output:

```html
Hello Kubernetes 🚀
```

---

# Common Issues

## Docker Permission Error

Error:

```bash
permission denied while trying to connect to docker.sock
```

Fix:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## Kubectl Permission Error

Error:

```bash
error loading config file
```

Fix:

```bash
sudo chmod 644 /etc/rancher/k3s/k3s.yaml
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
```

---

## ErrImageNeverPull

Verify Image Exists:

```bash
docker images
```

Delete Deployment:

```bash
kubectl delete deployment my-web-app
```

Recreate Deployment:

```bash
kubectl apply -f deployment.yaml
```

---

# Useful Commands

Check Nodes:

```bash
kubectl get nodes
```

Check Pods:

```bash
kubectl get pods
```

Check Services:

```bash
kubectl get svc
```

Check Deployments:

```bash
kubectl get deployments
```

Describe Pod:

```bash
kubectl describe pod <pod-name>
```

View Logs:

```bash
kubectl logs <pod-name>
```

Delete Deployment:

```bash
kubectl delete deployment my-web-app
```

Delete Service:

```bash
kubectl delete svc my-web-app
```

---

# Project Structure

```text
k8s-deployment-project/
│
├── index.html
├── Dockerfile
└── deployment.yaml
```

---

# Final Result

✅ Docker Installed

✅ K3s Installed

✅ Docker Image Built

✅ Kubernetes Deployment Created

✅ Service Exposed

✅ Application Running

Output:

```html
Hello Kubernetes 🚀
```

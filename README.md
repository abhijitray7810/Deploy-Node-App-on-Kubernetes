# Nautilus Node Application - Kubernetes Deployment

This repository contains the configuration and deployment files for the Nautilus Node.js application on Kubernetes cluster.

## 📋 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Deployment Instructions](#deployment-instructions)
- [Verification](#verification)
- [Access the Application](#access-the-application)
- [Troubleshooting](#troubleshooting)
- [Configuration Details](#configuration-details)

## 🎯 Overview

This project deploys a Node.js application on a Kubernetes cluster with the following specifications:

- **Image**: `gcr.io/kodekloud/centos-ssh-enabled:node`
- **Replicas**: 2
- **Service Type**: NodePort
- **Target Port**: 8080
- **Node Port**: 30012

## ✅ Prerequisites

- Kubernetes cluster up and running
- `kubectl` configured to work with your cluster (available on jump_host)
- Access to the jump_host terminal

## 📁 Project Structure

```
nautilus-node-app/
├── Dockerfile                 # Docker image configuration
├── node-app.yaml             # Kubernetes deployment and service
├── package.json              # Node.js dependencies
├── server.js                 # Application entry point
└── README.md                 # This file
```

## 🚀 Deployment Instructions
![image](https://github.com/abhijitray7810/Deploy-Node-App-on-Kubernetes/blob/2149ab54789d9f91a06bfbb858e185ccd6011a6c/project/Server.png)
### Step 1: Clone or Create Configuration Files

Save the Kubernetes configuration to a file named `node-app.yaml`:

```bash
cat > node-app.yaml << 'EOF'
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app-deployment
  labels:
    app: node-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app-container
        image: gcr.io/kodekloud/centos-ssh-enabled:node
        ports:
        - containerPort: 8080

---
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
  labels:
    app: node-app
spec:
  type: NodePort
  selector:
    app: node-app
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30012
    protocol: TCP
EOF
```

### Step 2: Apply the Configuration

Deploy the application to Kubernetes:

```bash
kubectl apply -f node-app.yaml
```

Expected output:
```
deployment.apps/node-app-deployment created
service/node-app-service created
```

### Step 3: Wait for Pods to be Ready

```bash
kubectl wait --for=condition=ready pod -l app=node-app --timeout=120s
```

## 🔍 Verification

### Check Deployment Status

```bash
kubectl get deployments
```

Expected output:
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
node-app-deployment   2/2     2            2           1m
```

### Check Pods Status

```bash
kubectl get pods -l app=node-app
```

Expected output:
```
NAME                                   READY   STATUS    RESTARTS   AGE
node-app-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          1m
node-app-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          1m
```

### Check Service Status

```bash
kubectl get service node-app-service
```

Expected output:
```
NAME               TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
node-app-service   NodePort   10.x.x.x        <none>        8080:30012/TCP   1m
```

### View Detailed Pod Information

```bash
kubectl describe pods -l app=node-app
```

### View Logs

```bash
# View logs from all pods
kubectl logs -l app=node-app

# View logs from a specific pod
kubectl logs <pod-name>

# Follow logs in real-time
kubectl logs -f -l app=node-app
```

## 🌐 Access the Application

### Option 1: Using NodeApp Button (Recommended)

Click on the **NodeApp** button on the top bar of your interface.

### Option 2: Using curl

```bash
# From jump_host or any node in the cluster
curl http://<node-ip>:30012

# Using localhost if on the node
curl http://localhost:30012
```

### Option 3: Port Forward (for testing)

```bash
kubectl port-forward service/node-app-service 8080:8080
```

Then access: `http://localhost:8080`

## 🛠️ Troubleshooting

### Pods Not Starting

Check pod events:
```bash
kubectl describe pod <pod-name>
```

Check pod logs:
```bash
kubectl logs <pod-name>
```

### ImagePullBackOff Error

Verify the image exists and is accessible:
```bash
kubectl describe pod <pod-name> | grep -A 5 Events
```

### Service Not Accessible

Verify service endpoints:
```bash
kubectl get endpoints node-app-service
```

Check if pods are selected by the service:
```bash
kubectl get pods -l app=node-app --show-labels
```

### Restart Deployment

```bash
kubectl rollout restart deployment/node-app-deployment
```

## 📝 Configuration Details

### Deployment Configuration

| Parameter | Value |
|-----------|-------|
| Name | node-app-deployment |
| Replicas | 2 |
| Image | gcr.io/kodekloud/centos-ssh-enabled:node |
| Container Port | 8080 |
| Labels | app=node-app |

### Service Configuration

| Parameter | Value |
|-----------|-------|
| Name | node-app-service |
| Type | NodePort |
| Port | 8080 |
| TargetPort | 8080 |
| NodePort | 30012 |
| Selector | app=node-app |

## 🧹 Cleanup

To remove the deployment and service:

```bash
kubectl delete -f node-app.yaml
```

Or delete individually:
```bash
kubectl delete deployment node-app-deployment
kubectl delete service node-app-service
```

## 📚 Additional Commands

### Scale the Deployment

```bash
# Scale up to 3 replicas
kubectl scale deployment node-app-deployment --replicas=3

# Scale down to 1 replica
kubectl scale deployment node-app-deployment --replicas=1
```

### Update the Image

```bash
kubectl set image deployment/node-app-deployment node-app-container=gcr.io/kodekloud/centos-ssh-enabled:node-v2
```

### View Rollout History

```bash
kubectl rollout history deployment/node-app-deployment
```

### Rollback to Previous Version

```bash
kubectl rollout undo deployment/node-app-deployment
```

## 👥 Team

- **Development Team**: Nautilus Development Team
- **DevOps Team**: DevOps Team

## 📄 License

This project is part of the Nautilus application deployment.

---

**Note**: Make sure all pods are in `Running` state before accessing the application. The kubectl on jump_host has been pre-configured to work with the Kubernetes cluster.

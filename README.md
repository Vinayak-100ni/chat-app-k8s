# Three-Tier Chat Application on Kubernetes

This repository contains the deployment files and configuration needed to run a **three-tier chat application** on **Kubernetes**. The application consists of:

* **Frontend** → React.js
* **Backend** → Node.js / Express.js
* **Database** → MongoDB

The deployment uses **Docker**, **Kubernetes Deployments**, **Services**, **Persistent Volumes (PV)**, **Persistent Volume Claims (PVC)**, **Secrets**, and **Ingress**.

---

## Architecture Overview

```
             +------------------+
             |    Frontend      |
             | (React, Nginx)   |
             +---------+--------+
                       |
                       v
             +---------+--------+
             |    Backend       |
             | (Node/Express)   |
             +---------+--------+
                       |
                       v
             +---------+--------+
             |   MongoDB DB     |
             |  (Persistent)    |
             +------------------+
```

* **Frontend** communicates with **Backend** via a Kubernetes Service.
* **Backend** connects to **MongoDB** using environment variables & secrets.
* **MongoDB** uses a **Persistent Volume (PV)** and **PVC** to persist data.
* **Ingress** is used to expose the app externally.

---

## Prerequisites

* Docker installed & configured
* Kubernetes cluster (Minikube / Kind / Cloud provider)
* kubectl configured
* DockerHub account (or any container registry)

---

## Deployment Steps

### 1. Build & Push Docker Images

#### Backend

```bash
docker build -t vinayak100ni/chat-app-k8s-backend:latest ./backend
docker push vinayak100ni/chat-app-k8s-backend:latest
```

#### Frontend

```bash
docker build -t vinayak100ni/chat-app-k8s-frontend:latest ./frontend
docker push vinayak100ni/chat-app-k8s-frontend:latest
```

> Ensure you are logged in to DockerHub using `docker login` before pushing images.

---

### 2. Apply Kubernetes Manifests

Run the following in order:

```bash
kubectl apply -f namespace.yaml
kubectl apply -f secrets.yaml
kubectl apply -f pv-mongo.yaml
kubectl apply -f pvc-mongo.yaml
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f mongodb-service.yaml
kubectl apply -f backend-deployment.yaml
kubectl apply -f backend-service.yaml
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml
kubectl apply -f app-ingress.yaml
```

---

### 3. Port Forwarding (For Local Testing)

You can forward services to local ports for testing:

```bash
kubectl get svc -n app

# Backend
kubectl port-forward svc/backend -n app 5001:5001 &

# Frontend
kubectl port-forward svc/frontend -n app 80:80 &

# MongoDB
kubectl port-forward svc/mongodb -n app 27017:27017 &
```

Now access:

* **Frontend** → [http://localhost:80](http://localhost:80)
* **Backend** → [http://localhost:5001](http://localhost:5001)
* **MongoDB** → mongodb://localhost:27017

---

### 4. Ingress Setup

If you have Ingress installed, access the app via hostname (configure DNS or add entry to `/etc/hosts`). Example:

```bash
chatapp.local -> frontend service
```

---

## Environment Variables & Secrets

The **backend** requires MongoDB connection details. These are stored in `secrets.yaml` and mounted in the deployment.

Example:

```yaml
env:
  - name: MONGO_URL
    valueFrom:
      secretKeyRef:
        name: mongo-secret
        key: mongo-uri
  - name: PORT
    value: "5001"
```

---

## Folder Structure

```
.
├── frontend/                     # React app code & Dockerfile
├── backend/                      # Node/Express app code & Dockerfile
├── app-ingress.yaml              # Ingress resource
├── backend-deployment.yaml       # Backend Deployment
├── backend-service.yaml          # Backend Service
├── frontend-deployment.yaml      # Frontend Deployment
├── frontend-service.yaml         # Frontend Service
├── mongodb-deployment.yaml       # MongoDB Deployment
├── mongodb-service.yaml          # MongoDB Service
├── namespace.yaml                # App namespace
├── pv-mongo.yaml                 # Persistent Volume
├── pvc-mongo.yaml                # Persistent Volume Claim
├── secrets.yaml                  # Secrets for DB connection
└── README.md
```

---

## Tech Stack

* **React.js** → UI (Frontend)
* **Node.js / Express.js** → Backend API
* **MongoDB** → Database
* **Docker** → Containerization
* **Kubernetes** → Orchestration
* **NGINX Ingress** → External access

---

## Future Enhancements

* Add **Horizontal Pod Autoscaling (HPA)** for backend & frontend.
* Add **ConfigMaps** for better configuration management.
* Implement **CI/CD pipeline** with Jenkins/GitHub Actions.
* Add **TLS certificates** for Ingress (HTTPS).

---

## Author

Deployed & maintained by **Vinayak** 🚀

# Lab 2: Why Use Kubernetes? Benefits, Use Cases & Real‑World Examples

## Objective
Understand **why Kubernetes is needed** by comparing manual container management vs Kubernetes orchestration.  
You’ll deploy a container manually with Docker, then deploy the same app with Kubernetes, and observe the differences.

## Prerequisites
- MicroK8s installed and running (`microk8s status --wait-ready`).  
- Docker installed (optional, for comparison).  

## Step 1: Run Container Manually (Docker)
```bash
docker run -d --name hello-docker -p 8080:80 nginx
```

Test access:
```bash
curl http://localhost:8080
```
Expected: NGINX welcome page HTML.

Stop and remove container:
```bash
docker rm -f hello-docker
```

## Step 2: Deploy Same App on Kubernetes
```bash
microk8s kubectl create deployment hello-k8s --image=nginx
microk8s kubectl expose deployment hello-k8s --port=80 --type=NodePort
```

Check resources:
```bash
microk8s kubectl get deployments
microk8s kubectl get pods
microk8s kubectl get svc
```

## Step 3: Test Kubernetes Service
Find NodePort:
```bash
microk8s kubectl get svc hello-k8s
```
Output shows `PORT(S)` like `80:30042/TCP`.  
Test access:
```bash
curl http://localhost:30042
```
Expected: NGINX welcome page HTML.


## Step 4: Clean Up
```bash
microk8s kubectl delete svc hello-k8s
microk8s kubectl delete deployment hello-k8s
```

## Testing Checklist
- [ ] Docker container runs and responds on port 8080.  
- [ ] Kubernetes Deployment + Service created successfully.  
- [ ] Pod responds via NodePort.  
- [ ] Resources cleaned up.  


## Key Learning
- Docker runs containers, but Kubernetes **orchestrates** them.  
- Kubernetes adds **scaling, self‑healing, and service discovery**.  
- This lab demonstrates why Kubernetes is needed beyond Docker.  


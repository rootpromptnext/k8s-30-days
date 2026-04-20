# Lab 11: Deployments in Kubernetes — Rolling Updates & Rollbacks

## Objective
Learn how **Deployments** manage application updates and rollbacks:
- Create a Deployment.  
- Perform a rolling update.  
- Observe version transition.  
- Roll back to a previous version.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with ReplicaSets and Pods.  

## Step 1: Create Initial Deployment
Create a file named `nginx-deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-demo
  template:
    metadata:
      labels:
        app: nginx-demo
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

Apply it:
```bash
microk8s kubectl apply -f nginx-deployment.yaml
microk8s kubectl get deployments
microk8s kubectl get pods
```

## Step 2: Expose Deployment
```bash
microk8s kubectl expose deployment nginx-deploy --port=80 --type=NodePort
microk8s kubectl get svc
```

Test access:
```bash
curl http://localhost:<NodePort>
```
Expected: NGINX welcome page HTML.


## Step 3: Perform Rolling Update
Update image version:
```bash
microk8s kubectl set image deployment/nginx-deploy nginx=nginx:1.23
```

Monitor rollout:
```bash
microk8s kubectl rollout status deployment/nginx-deploy
microk8s kubectl get pods -l app=nginx-demo
```
Observe old Pods terminating and new Pods starting gradually.

## Step 4: Roll Back Deployment
If something goes wrong, roll back:
```bash
microk8s kubectl rollout undo deployment/nginx-deploy
```

Verify rollback:
```bash
microk8s kubectl describe deployment nginx-deploy | grep Image
```
Expected: Image reverted to `nginx:1.21`.

---

## Step 5: Clean Up
```bash
microk8s kubectl delete svc nginx-deploy
microk8s kubectl delete deployment nginx-deploy
```

---

## Testing Checklist
- [ ] Deployment created successfully.  
- [ ] Service exposed and accessible.  
- [ ] Rolling update completed with new image version.  
- [ ] Rollback restored previous version.  
- [ ] Resources cleaned up.  


## Key Learning
- Deployments manage ReplicaSets automatically.  
- Rolling updates replace Pods gradually to avoid downtime.  
- Rollbacks restore previous stable versions.  
- Kubernetes ensures **zero‑downtime updates** and **version control** for workloads.  

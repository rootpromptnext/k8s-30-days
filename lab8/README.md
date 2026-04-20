# Lab 8: Deploying Your First Application on Kubernetes (NGINX)

## Objective
Deploy your first real application (NGINX web server) on Kubernetes using MicroK8s:
- Create a Deployment.  
- Expose it via a Service.  
- Test external access.  
- Scale and clean up.  


## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Basic familiarity with Pods and Deployments.  


## Step 1: Create Deployment
Deploy NGINX:
```bash
microk8s kubectl create deployment nginx-app --image=nginx
```

Verify Deployment:
```bash
microk8s kubectl get deployments
microk8s kubectl get pods
```

## Step 2: Expose Deployment
Expose as a NodePort Service:
```bash
microk8s kubectl expose deployment nginx-app --port=80 --type=NodePort
```

Check Service:
```bash
microk8s kubectl get svc
```
Note the `PORT(S)` field (e.g., `80:30042/TCP`).

## Step 3: Test Access
Use curl to test:
```bash
curl http://localhost:<NodePort>
```
Expected: NGINX welcome page HTML.

Check Pod logs:
```bash
microk8s kubectl logs -l app=nginx-app
```

## Step 4: Scale Deployment
Increase replicas:
```bash
microk8s kubectl scale deployment nginx-app --replicas=3
microk8s kubectl get pods
```
You should see 3 Pods running.

## Step 5: Clean Up
Delete resources:
```bash
microk8s kubectl delete svc nginx-app
microk8s kubectl delete deployment nginx-app
```

## Testing Checklist
- [ ] Deployment created successfully.  
- [ ] Service exposed with NodePort.  
- [ ] NGINX accessible via curl.  
- [ ] Deployment scaled to multiple Pods.  
- [ ] Resources cleaned up.  

## Key Learning
- Deployments manage Pods and ensure desired state.  
- Services expose Deployments to external traffic.  
- Scaling is declarative — Kubernetes ensures the right number of Pods.  
- This is the **first full app deployment workflow** in Kubernetes.  

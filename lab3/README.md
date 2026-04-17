# Lab 3: Exploring Cluster Objects (Nodes, Pods, Deployments)

## Objective
Learn how to inspect and understand the **core Kubernetes objects**:
- Nodes (worker machines in the cluster)  
- Pods (smallest deployable unit)  
- Deployments (manages ReplicaSets and Pods)  

## Prerequisites
- MicroK8s installed and running (`microk8s status --wait-ready`).  
- Familiarity with basic terminal commands.  


## Step 1: Inspect Nodes
List all nodes in the cluster:
```bash
microk8s kubectl get nodes
```

Detailed information about a node:
```bash
microk8s kubectl describe node <node-name>
```

Check node resources:
```bash
microk8s kubectl top nodes
```

## Step 2: Explore Pods
Deploy a sample Pod:
```bash
microk8s kubectl run demo-pod --image=nginx
```

List Pods:
```bash
microk8s kubectl get pods
```

Describe Pod details:
```bash
microk8s kubectl describe pod demo-pod
```

Check Pod logs:
```bash
microk8s kubectl logs demo-pod
```

## Step 3: Work with Deployments
Create a Deployment:
```bash
microk8s kubectl create deployment demo-deploy --image=nginx
```

List Deployments:
```bash
microk8s kubectl get deployments
```

Describe Deployment:
```bash
microk8s kubectl describe deployment demo-deploy
```

Scale Deployment:
```bash
microk8s kubectl scale deployment demo-deploy --replicas=3
microk8s kubectl get pods
```

## Step 4: Test Connectivity
Expose Deployment as a Service:
```bash
microk8s kubectl expose deployment demo-deploy --port=80 --type=NodePort
microk8s kubectl get svc
```

Test access:
```bash
curl http://localhost:<NodePort>
```
Expected: NGINX welcome page HTML.

## Step 5: Clean Up
```bash
microk8s kubectl delete svc demo-deploy
microk8s kubectl delete deployment demo-deploy
microk8s kubectl delete pod demo-pod
```

## Testing Checklist
- [ ] Node listed and described successfully.  
- [ ] Pod created, logs accessible.  
- [ ] Deployment created and scaled.  
- [ ] Service exposed and accessible via NodePort.  
- [ ] All resources cleaned up.  


## Key Learning
- **Nodes** are the worker machines in Kubernetes.  
- **Pods** are the smallest deployable unit.  
- **Deployments** manage Pods and ensure desired state.  
- Services expose Deployments to external traffic.  


# Lab 1: What is Kubernetes? A Complete Introduction for Beginners

## Objective
Get hands‑on with Kubernetes for the first time:
- Install MicroK8s (local Kubernetes distribution).  
- Verify cluster health and components.  
- Deploy a simple Pod.  
- Test Pod functionality.  
- Clean up resources.  


## Prerequisites
- Ubuntu/Linux system (or Windows/Mac with Multipass/VM).  
- Basic knowledge of terminal commands.  
- Internet connection.  

## Step 1: Install MicroK8s
```bash
sudo snap install microk8s --classic
```

Add your user to the MicroK8s group (so you don’t need `sudo`):
```bash
sudo usermod -a -G microk8s $USER
newgrp microk8s
```

## Step 2: Verify Cluster Status
Check if MicroK8s is ready:
```bash
microk8s status --wait-ready
```

Inspect nodes:
```bash
microk8s kubectl get nodes
```

Inspect system Pods:
```bash
microk8s kubectl get pods -A
```

## Step 3: Deploy Your First Pod
Run a simple NGINX Pod:
```bash
microk8s kubectl run hello-pod --image=nginx
```

Check Pod status:
```bash
microk8s kubectl get pods
microk8s kubectl describe pod hello-pod
```

## Step 4: Test Pod Functionality
Execute a command inside the Pod:
```bash
microk8s kubectl exec hello-pod -- curl localhost
```
Expected output: HTML response from NGINX (`<html>...Welcome to nginx!...`).

View Pod logs:
```bash
microk8s kubectl logs hello-pod
```


## Step 5: Clean Up
Delete the Pod:
```bash
microk8s kubectl delete pod hello-pod
```

Confirm cleanup:
```bash
microk8s kubectl get pods
```

## Testing Checklist
- [ ] MicroK8s installed successfully.  
- [ ] Cluster shows `Ready` node(s).  
- [ ] System Pods running in `kube-system` namespace.  
- [ ] NGINX Pod deployed and responding to `curl localhost`.  
- [ ] Pod logs accessible.  
- [ ] Pod deleted successfully.  

## Key Learning
- Kubernetes is a **cluster orchestration system**.  
- MicroK8s provides a lightweight local cluster.  
- Pods are the smallest deployable unit in Kubernetes.  
- You can interact with Pods using `kubectl` commands.  

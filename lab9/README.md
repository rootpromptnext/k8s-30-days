# Lab 9: Pod Manifest Demo

## Objective
Learn how to create and manage Pods using **YAML manifests**:
- Write a Pod manifest.  
- Apply it to the cluster.  
- Inspect Pod details.  
- Test connectivity.  
- Clean up resources.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with basic `kubectl` commands.  

## Step 1: Write Pod Manifest
Create a file named `nginx-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx-demo
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

## Step 2: Apply Manifest
```bash
microk8s kubectl apply -f nginx-pod.yaml
```

Check Pod status:
```bash
microk8s kubectl get pods
```

## Step 3: Inspect Pod
Describe Pod:
```bash
microk8s kubectl describe pod nginx-pod
```

View logs:
```bash
microk8s kubectl logs nginx-pod
```

Exec into Pod:
```bash
microk8s kubectl exec -it nginx-pod -- /bin/bash
```

## Step 4: Test Connectivity
Expose Pod temporarily:
```bash
microk8s kubectl expose pod nginx-pod --port=80 --type=NodePort
microk8s kubectl get svc
```

Test access:
```bash
curl http://localhost:<NodePort>
```
Expected: NGINX welcome page HTML.

## Step 5: Clean Up
```bash
microk8s kubectl delete svc nginx-pod
microk8s kubectl delete pod nginx-pod
```

## Testing Checklist
- [ ] Pod manifest written and applied successfully.  
- [ ] Pod running and visible in `kubectl get pods`.  
- [ ] Pod logs accessible.  
- [ ] Pod responds via NodePort.  
- [ ] Pod and Service cleaned up.  


## Key Learning
- Pods can be defined declaratively with YAML manifests.  
- Labels help identify and group Pods.  
- `kubectl apply` creates resources from manifests.  
- Services expose Pods for external access.  


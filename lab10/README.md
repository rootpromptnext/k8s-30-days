# Lab 10: ReplicaSet Scaling

## Objective
Understand how **ReplicaSets** ensure high availability and scalability:
- Create a ReplicaSet using a YAML manifest.  
- Observe automatic Pod management.  
- Scale replicas up and down.  
- Test and clean up.  


## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods and Deployments.  


## Step 1: Create ReplicaSet Manifest
Create a file named `nginx-replicaset.yaml`:
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-rs
  template:
    metadata:
      labels:
        app: nginx-rs
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

## Step 2: Apply Manifest
```bash
microk8s kubectl apply -f nginx-replicaset.yaml
```

Check ReplicaSet and Pods:
```bash
microk8s kubectl get rs
microk8s kubectl get pods -l app=nginx-rs
```

## Step 3: Observe Self‑Healing
Delete one Pod manually:
```bash
microk8s kubectl delete pod <pod-name>
```

Check again:
```bash
microk8s kubectl get pods -l app=nginx-rs
```
Kubernetes automatically creates a new Pod to maintain 3 replicas.

## Step 4: Scale ReplicaSet
Edit replicas count:
```bash
microk8s kubectl scale rs nginx-replicaset --replicas=5
microk8s kubectl get pods -l app=nginx-rs
```

Scale down:
```bash
microk8s kubectl scale rs nginx-replicaset --replicas=2
microk8s kubectl get pods -l app=nginx-rs
```

## Step 5: Test Connectivity
Expose one Pod temporarily:
```bash
microk8s kubectl expose pod <pod-name> --port=80 --type=NodePort
microk8s kubectl get svc
```

Test access:
```bash
curl http://localhost:<NodePort>
```
Expected: NGINX welcome page HTML.


## Step 6: Clean Up
```bash
microk8s kubectl delete svc <pod-name>
microk8s kubectl delete rs nginx-replicaset
```

## Testing Checklist
- [ ] ReplicaSet created successfully.  
- [ ] 3 Pods running initially.  
- [ ] Self‑healing observed after Pod deletion.  
- [ ] Scaling up/down works correctly.  
- [ ] Resources cleaned up.  

## Key Learning
- ReplicaSets maintain a **desired number of Pod replicas**.  
- Kubernetes automatically replaces failed Pods.  
- Scaling is declarative — you define desired replicas, Kubernetes enforces it.  
- ReplicaSets are the foundation for Deployments.  


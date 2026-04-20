# Lab 12: StatefulSet with Headless Service

## Objective
Learn how to deploy a **StatefulSet** with a **headless Service**:
- Understand why StatefulSets are used for stateful apps (databases, queues).  
- Create a headless Service for stable DNS identities.  
- Deploy a StatefulSet with multiple replicas.  
- Inspect Pod identities and connectivity.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods, ReplicaSets, and Deployments.  
## Step 1: Create Headless Service
Create a file named `nginx-headless-svc.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-headless
  labels:
    app: nginx-sts
spec:
  clusterIP: None   # Headless service
  selector:
    app: nginx-sts
  ports:
  - port: 80
    name: web
```

Apply it:
```bash
microk8s kubectl apply -f nginx-headless-svc.yaml
microk8s kubectl get svc
```

## Step 2: Create StatefulSet
Create a file named `nginx-statefulset.yaml`:
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: nginx-sts
spec:
  serviceName: "nginx-headless"
  replicas: 3
  selector:
    matchLabels:
      app: nginx-sts
  template:
    metadata:
      labels:
        app: nginx-sts
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Apply it:
```bash
microk8s kubectl apply -f nginx-statefulset.yaml
microk8s kubectl get statefulsets
microk8s kubectl get pods -l app=nginx-sts
```

## Step 3: Inspect Pod Identity
Pods in StatefulSet get **stable names**:
```bash
microk8s kubectl get pods -l app=nginx-sts
```
Expected:  
- `nginx-sts-0`  
- `nginx-sts-1`  
- `nginx-sts-2`

Check DNS resolution:
```bash
microk8s kubectl exec nginx-sts-0 -- nslookup nginx-sts-1.nginx-headless
```
Each Pod has a **stable DNS entry** via the headless Service.


## Step 4: Test Connectivity
Exec into one Pod and curl another:
```bash
microk8s kubectl exec -it nginx-sts-0 -- curl nginx-sts-1.nginx-headless
```
Expected: NGINX HTML response.


## Step 5: Clean Up
```bash
microk8s kubectl delete statefulset nginx-sts
microk8s kubectl delete svc nginx-headless
```

---

## Testing Checklist
- [ ] Headless Service created successfully.  
- [ ] StatefulSet deployed with 3 Pods.  
- [ ] Pods have stable names (`nginx-sts-0`, `nginx-sts-1`, etc.).  
- [ ] DNS resolution works between Pods.  
- [ ] Resources cleaned up.  


## Key Learning
- **Deployments** are for stateless apps; **StatefulSets** are for stateful apps needing stable identity.  
- Headless Services provide **stable DNS names** for Pods.  
- StatefulSets ensure predictable Pod naming and ordering.  
- Commonly used for databases, message queues, and clustered apps.  

# Lab 17: Pod‑to‑Pod Networking Demo

## Objective
Understand **Pod‑to‑Pod communication** in Kubernetes:
- Deploy two Pods.  
- Verify Pod IPs.  
- Test connectivity using `curl` or `ping`.  
- Observe how Kubernetes networking enables communication without manual setup.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods and Services.  

## Step 1: Deploy Two Pods
Create Pod A:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-a
  labels:
    app: demo-a
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

Create Pod B:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-b
  labels:
    app: demo-b
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]
```

Apply both:
```bash
microk8s kubectl apply -f pod-a.yaml
microk8s kubectl apply -f pod-b.yaml
microk8s kubectl get pods -o wide
```

Note Pod IPs in the output.

## Step 2: Test Pod‑to‑Pod Connectivity
Exec into Pod B:
```bash
microk8s kubectl exec -it pod-b -- sh
```

From inside Pod B, curl Pod A:
```bash
curl http://<PodA-IP>
```
Expected: NGINX HTML response.

Ping Pod A:
```bash
ping <PodA-IP>
```
Shows Pod‑to‑Pod reachability.

## Step 3: DNS‑Based Communication
Instead of Pod IPs, use Service + DNS.

Create Service for Pod A:
```bash
microk8s kubectl expose pod pod-a --port=80 --name=svc-a
microk8s kubectl get svc svc-a
```

From Pod B:
```bash
curl http://svc-a
```
Works via Kubernetes DNS, not raw IP.

## Step 4: Clean Up
```bash
microk8s kubectl delete pod pod-a pod-b
microk8s kubectl delete svc svc-a
```

## Testing Checklist
- [ ] Two Pods deployed successfully.  
- [ ] Pod IPs retrieved with `kubectl get pods -o wide`.  
- [ ] Pod B accessed Pod A via IP.  
- [ ] Pod B accessed Pod A via Service DNS.  
- [ ] Resources cleaned up.  


## Key Learning
- Kubernetes provides **flat networking**: every Pod gets an IP, routable within the cluster.  
- Pods can communicate directly via IP, but IPs are ephemeral.  
- Services + DNS provide stable communication endpoints.  
- Pod‑to‑Pod networking is the foundation for microservices in Kubernetes.  


# Lab 23: Pod Security Standards & Network Policies

## Objective
Learn how Kubernetes secures workloads:
- Apply **Pod Security Standards (PSS)** to namespaces.  
- Restrict Pod capabilities with policies.  
- Create **NetworkPolicies** to control Pod‑to‑Pod communication.  
- Test enforcement and cleanu

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods, Services, and RBAC.  
- Metrics server enabled (optional for testing).  

## Step 1: Enable Pod Security Admission (PSA)
MicroK8s supports Pod Security Standards via namespace labels.

Create namespace:
```bash
microk8s kubectl create namespace secure-demo
```

Label namespace with **restricted** policy:
```bash
microk8s kubectl label namespace secure-demo pod-security.kubernetes.io/enforce=restricted
```

Verify:
```bash
microk8s kubectl get ns --show-labels
```

## Step 2: Test Pod Security Enforcement
Try deploying a privileged Pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privileged-pod
  namespace: secure-demo
spec:
  containers:
  - name: demo
    image: busybox
    command: ["sleep", "3600"]
    securityContext:
      privileged: true
```

Apply:
```bash
microk8s kubectl apply -f privileged-pod.yaml
```

Expected: Pod creation denied due to **restricted policy**.

---

## Step 3: Create NetworkPolicy
Create a Deployment:
```bash
microk8s kubectl create deployment net-demo --image=nginx -n secure-demo
microk8s kubectl expose deployment net-demo --port=80 -n secure-demo
```

Create a NetworkPolicy `deny-all.yaml`:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: secure-demo
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

Apply:
```bash
microk8s kubectl apply -f deny-all.yaml
```

All traffic to Pods in `secure-demo` is blocked.

## Step 4: Allow Specific Traffic
Create `allow-nginx.yaml`:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-nginx
  namespace: secure-demo
spec:
  podSelector:
    matchLabels:
      app: net-demo
  ingress:
  - from:
    - podSelector:
        matchLabels:
          access: allowed
    ports:
    - protocol: TCP
      port: 80
```

Apply:
```bash
microk8s kubectl apply -f allow-nginx.yaml
```

Only Pods with label `access=allowed` can connect to `net-demo`.


## Step 5: Clean Up
```bash
microk8s kubectl delete ns secure-demo
```

## Testing Checklist
- [ ] Namespace labeled with Pod Security Standards.  
- [ ] Privileged Pod creation denied.  
- [ ] NetworkPolicy applied to block traffic.  
- [ ] Allowed Pods accessed service successfully.  
- [ ] Resources cleaned up.  


## Key Learning
- **Pod Security Standards (PSS)** enforce baseline security at namespace level.  
- Policies prevent privileged Pods or unsafe configurations.  
- **NetworkPolicies** control ingress/egress traffic between Pods.  
- Together, they provide **defense‑in‑depth** for Kubernetes workloads.  


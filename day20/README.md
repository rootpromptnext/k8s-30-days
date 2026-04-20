# Lab 20: Resource Requests, Limits & Quotas

## Objective
Learn how Kubernetes controls resource usage:
- Define **CPU/memory requests** and **limits** for Pods.  
- Observe scheduling behavior.  
- Apply **ResourceQuota** at namespace level.  
- Test and clean up.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods and YAML manifests.  

---

## Step 1: Pod with Resource Requests & Limits
Create a file named `pod-resources.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-resources-demo
spec:
  containers:
  - name: stress
    image: busybox
    command: ["sh", "-c", "echo Running && sleep 3600"]
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

Apply it:
```bash
microk8s kubectl apply -f pod-resources.yaml
microk8s kubectl describe pod pod-resources-demo
```

Requests = minimum guaranteed resources.  
Limits = maximum allowed resources.

---

## Step 2: Observe Scheduling
Pods are scheduled only if nodes have enough resources to satisfy **requests**.  
Check node capacity:
```bash
microk8s kubectl describe node
```

---

## Step 3: ResourceQuota in Namespace
Create a namespace:
```bash
microk8s kubectl create namespace quota-demo
```

Create quota manifest `quota.yaml`:
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: demo-quota
  namespace: quota-demo
spec:
  hard:
    pods: "2"
    requests.cpu: "1"
    requests.memory: "1Gi"
    limits.cpu: "2"
    limits.memory: "2Gi"
```

Apply it:
```bash
microk8s kubectl apply -f quota.yaml
microk8s kubectl describe quota -n quota-demo
```

---

## Step 4: Test Quota Enforcement
Try creating multiple Pods in `quota-demo` namespace:
```bash
microk8s kubectl run pod1 -n quota-demo --image=nginx
microk8s kubectl run pod2 -n quota-demo --image=nginx
microk8s kubectl run pod3 -n quota-demo --image=nginx
```

The third Pod fails because quota allows only 2 Pods.

---

## Step 5: Clean Up
```bash
microk8s kubectl delete pod pod-resources-demo
microk8s kubectl delete ns quota-demo
```

---

## Testing Checklist
- [ ] Pod with resource requests/limits created successfully.  
- [ ] Node capacity inspected.  
- [ ] ResourceQuota applied to namespace.  
- [ ] Quota enforcement observed (Pod creation blocked).  
- [ ] Resources cleaned up.  


## Key Learning
- **Requests** guarantee minimum resources for scheduling.  
- **Limits** cap maximum resource usage per container.  
- **ResourceQuotas** enforce limits across a namespace.  
- These mechanisms prevent resource starvation and ensure fair usage in multi‑tenant clusters.  


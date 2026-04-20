# Lab 21: Metrics & Optimization

## Objective
Learn how Kubernetes uses metrics to optimize workloads:
- Enable metrics server in MicroK8s.  
- Collect Pod and node resource usage.  
- Use metrics for optimization decisions.  
- Explore Horizontal Pod Autoscaler (HPA) briefly.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with resource requests/limits (Lab 20).  

## Step 1: Enable Metrics Server
MicroK8s includes an add‑on:
```bash
microk8s enable metrics-server
```

Verify:
```bash
microk8s kubectl get pods -n kube-system | grep metrics
```
## Step 2: Deploy Sample App
```bash
microk8s kubectl create deployment metrics-demo --image=nginx
microk8s kubectl scale deployment metrics-demo --replicas=2
```

## Step 3: Collect Metrics
Check Pod resource usage:
```bash
microk8s kubectl top pods
```

Check node resource usage:
```bash
microk8s kubectl top nodes
```

Displays CPU and memory consumption.

## Step 4: Optimize with HPA (Horizontal Pod Autoscaler)
Create HPA:
```bash
microk8s kubectl autoscale deployment metrics-demo --cpu-percent=50 --min=2 --max=5
```

Check HPA:
```bash
microk8s kubectl get hpa
```

HPA scales Pods based on CPU usage metrics.

## Step 5: Generate Load 
Run a busybox Pod to stress test:
```bash
microk8s kubectl run load-generator --image=busybox -- \
  sh -c "while true; do wget -q -O- http://metrics-demo; done"
```

Observe scaling:
```bash
microk8s kubectl get pods
```

## Step 6: Clean Up
```bash
microk8s kubectl delete deployment metrics-demo
microk8s kubectl delete hpa metrics-demo
microk8s kubectl delete pod load-generator
```

## Testing Checklist
- [ ] Metrics server enabled successfully.  
- [ ] Pod and node metrics collected with `kubectl top`.  
- [ ] HPA created and observed.  
- [ ] Load generator triggered scaling.  
- [ ] Resources cleaned up.  


## Key Learning
- Metrics server provides **CPU/memory usage data** for Pods and nodes.  
- Metrics are essential for **monitoring and optimization**.  
- HPA uses metrics to scale Pods automatically.  
- Optimization ensures efficient resource utilization and application performance.  


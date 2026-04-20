# Lab 25: Autoscaling (HPA, VPA, Cluster Autoscaler)

## Objective
Understand Kubernetes autoscaling mechanisms:
- **Horizontal Pod Autoscaler (HPA)** → scales Pods based on metrics.  
- **Vertical Pod Autoscaler (VPA)** → adjusts Pod resource requests/limits.  
- **Cluster Autoscaler (CA)** → adds/removes nodes based on demand.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Metrics server enabled (Lab 21).  
- Familiarity with resource requests/limits (Lab 20).  

## Step 1: Horizontal Pod Autoscaler (HPA)
Deploy sample app:
```bash
microk8s kubectl create deployment hpa-demo --image=nginx
microk8s kubectl expose deployment hpa-demo --port=80
```

Create HPA:
```bash
microk8s kubectl autoscale deployment hpa-demo --cpu-percent=50 --min=2 --max=5
microk8s kubectl get hpa
```

HPA scales Pods based on CPU usage metrics.

Generate load:
```bash
microk8s kubectl run load-generator --image=busybox -- \
  sh -c "while true; do wget -q -O- http://hpa-demo; done"
```

Observe scaling:
```bash
microk8s kubectl get pods
```

## Step 2: Vertical Pod Autoscaler (VPA)
> Note: VPA is not bundled with MicroK8s by default; install via upstream manifests.

Install VPA components:
```bash
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/download/vpa-release-0.13.0/vpa.yaml
```

Create VPA manifest `vpa.yaml`:
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: vpa-demo
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind:       Deployment
    name:       hpa-demo
  updatePolicy:
    updateMode: "Auto"
```

Apply:
```bash
microk8s kubectl apply -f vpa.yaml
```

VPA adjusts Pod resource requests/limits automatically based on usage.

## Step 3: Cluster Autoscaler (CA)
> Conceptual in MicroK8s; practical in cloud providers (EKS, GKE, AKS).

Install Cluster Autoscaler (example for AWS/EKS):
```bash
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/download/cluster-autoscaler-1.29.0/cluster-autoscaler.yaml
```

Configure with node group:
- CA monitors pending Pods.  
- If nodes lack capacity, CA provisions new nodes.  
- If nodes are underutilized, CA removes them.  

In MicroK8s (single‑node), CA is not functional but can be simulated with multi‑node setups.

## Step 4: Clean Up
```bash
microk8s kubectl delete deployment hpa-demo
microk8s kubectl delete svc hpa-demo
microk8s kubectl delete hpa hpa-demo
microk8s kubectl delete pod load-generator
```

(Optional for VPA/CA):
```bash
microk8s kubectl delete vpa vpa-demo
```

## Testing Checklist
- [ ] HPA created and observed scaling.  
- [ ] VPA installed and adjusted Pod resources.  
- [ ] Cluster Autoscaler concept understood (cloud‑based).  
- [ ] Resources cleaned up.  

## Key Learning
- **HPA** → scales Pods horizontally based on metrics.  
- **VPA** → adjusts Pod resource requests/limits vertically.  
- **Cluster Autoscaler** → scales nodes in/out based on cluster demand.  
- Together, they ensure **efficient resource utilization** and **high availability**.  

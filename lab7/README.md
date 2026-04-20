# Lab 7: Setting Up Kubernetes — Local vs Managed Clusters

## Objective
- Learn how to set up a **local Kubernetes cluster** with MicroK8s.  
- Understand how **managed Kubernetes services** (EKS, GKE, AKS) differ.  
- Compare operational responsibilities between local and managed clusters.  

## Prerequisites
- Ubuntu/Linux system with Snap installed.  
- MicroK8s installed (`sudo snap install microk8s --classic`).  
- Cloud account (AWS/GCP/Azure) if you want to explore managed clusters.  

## Step 1: Set Up Local Cluster (MicroK8s)
Install MicroK8s:
```bash
sudo snap install microk8s --classic
```

Add user to group:
```bash
sudo usermod -a -G microk8s $USER
newgrp microk8s
```

Check status:
```bash
microk8s status --wait-ready
```

Inspect nodes and system Pods:
```bash
microk8s kubectl get nodes
microk8s kubectl get pods -n kube-system
```

You’ll see **one node** (local machine) with both control plane + worker services.

## Step 2: Deploy Sample App on Local Cluster
```bash
microk8s kubectl create deployment local-nginx --image=nginx
microk8s kubectl expose deployment local-nginx --port=80 --type=NodePort
microk8s kubectl get svc
```

Test access:
```bash
curl http://localhost:<NodePort>
```

## Step 3: Managed Cluster (Conceptual Demo)
### AWS EKS
- Control plane managed by AWS (you don’t see master nodes).  
- You provision worker nodes (EC2 or Fargate).  
- `kubectl get nodes` shows only worker nodes.  

### GCP GKE
- Google manages control plane.  
- You manage worker nodes (GCE VMs).  

### Azure AKS
- Azure manages control plane.  
- You manage worker nodes (VM Scale Sets).  

In all cases, you use `kubectl` the same way, but the **cloud provider handles upgrades, HA, and scaling of the control plane**.

## Step 4: Compare Local vs Managed
| Feature              | MicroK8s (Local) | Managed (EKS/GKE/AKS) |
|----------------------|------------------|------------------------|
| Control Plane        | Runs locally     | Managed by provider    |
| Worker Nodes         | Local machine    | Cloud VMs/Fargate      |
| Scaling              | Manual           | Auto‑scaling supported |
| Upgrades             | Manual           | Automated              |
| Cost                 | Free (local)     | Pay per node/hour      |

## Step 5: Clean Up Local Cluster
```bash
microk8s kubectl delete svc local-nginx
microk8s kubectl delete deployment local-nginx
```

## Testing Checklist
- [ ] MicroK8s installed and running.  
- [ ] Local node visible with `kubectl get nodes`.  
- [ ] System Pods visible in `kube-system`.  
- [ ] Sample app deployed and accessible via NodePort.  
- [ ] Resources cleaned up.  
- [ ] Understood differences between local and managed clusters.  

---

## Key Learning
- MicroK8s provides a **lightweight local cluster** for learning and testing.  
- Managed services (EKS/GKE/AKS) handle **control plane operations** for you.  
- Kubernetes commands (`kubectl`) remain the same across environments.  
- Choice depends on use case: local for learning/dev, managed for production.

  

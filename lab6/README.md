# Lab 6: Kubernetes Architecture Inspection (Updated)

## Objective
Understand the **Kubernetes architecture** by inspecting:
- Control plane components (API server, scheduler, controller manager, etcd).  
- Worker node components (kubelet, kube‑proxy, container runtime).  
- How this looks in **MicroK8s (single‑node)** vs **vanilla Kubernetes/EKS (multi‑node)**.  


## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Basic knowledge of Pods and nodes.  


## Step 1: Inspect MicroK8s Cluster
MicroK8s runs **control plane + worker services on the same node**.

Check cluster status:
```bash
microk8s status --wait-ready
```

List nodes:
```bash
microk8s kubectl get nodes
```
You’ll see **one node** (your local machine), marked `Ready`.

Inspect system Pods:
```bash
microk8s kubectl get pods -n kube-system
```
You’ll see Pods like:
- `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, `etcd` (control plane).  
- `kube-proxy`, `coredns`, `metrics-server` (worker services).  


## Step 2: Deploy a Test Pod
```bash
microk8s kubectl run arch-demo --image=nginx
microk8s kubectl get pods
microk8s kubectl describe pod arch-demo
```
Observe:
- **API server** accepts request.  
- **Scheduler** assigns Pod to the single node.  
- **Kubelet** runs the Pod.  
- **Controller manager** ensures desired state.  

## Step 3: Vanilla Kubernetes (Multi‑Node)
In a standard kubeadm cluster:
- **Control plane nodes** run API server, scheduler, controller manager, etcd.  
- **Worker nodes** run kubelet, kube‑proxy, and workloads.  
- `kubectl get nodes` shows multiple nodes (e.g., `master-1`, `worker-1`, `worker-2`).  
- Scheduler decides which worker node gets the Pod.  


## Step 4: EKS (Managed Kubernetes)
In AWS EKS:
- Control plane is **managed by AWS** (you don’t see control plane nodes).  
- You only manage **worker nodes** (EC2 instances or Fargate).  
- `kubectl get nodes` shows worker nodes only.  
- Control plane scaling, upgrades, and HA are handled by AWS.  


## Cleanup
```bash
microk8s kubectl delete pod arch-demo
```


## Testing Checklist
- [ ] MicroK8s shows one node with both control plane + worker services.  
- [ ] System Pods visible in `kube-system`.  
- [ ] Test Pod deployed and described successfully.  
- [ ] Pod deleted successfully.  
- [ ] Understood difference between MicroK8s (single node), vanilla Kubernetes (multi‑node), and EKS (managed control plane).  


## Key Learning
- MicroK8s collapses control plane + worker into a **single node** for simplicity.  
- Vanilla Kubernetes separates **control plane nodes** and **worker nodes**.  
- EKS hides the control plane — you only manage worker nodes.  
- Regardless of setup, the **architecture pattern is the same**: control plane manages state, worker nodes run workloads.  


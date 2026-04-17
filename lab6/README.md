# Lab 6: Kubernetes Architecture Inspection

## Objective
Understand the **Kubernetes architecture** by inspecting:
- Control plane components (API server, scheduler, controller manager, etcd).  
- Worker node components (kubelet, kube‑proxy, container runtime).  
- System Pods running in the `kube-system` namespace.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Basic knowledge of Pods and nodes.  

## Step 6: Deploy a Test Pod
```bash
microk8s kubectl run arch-demo --image=nginx
microk8s kubectl get pods
microk8s kubectl describe pod arch-demo
```
- Observe how the **API server** accepts the request,  
- **Scheduler** assigns Pod to a node,  
- **Kubelet** runs the Pod,  
- **Controller manager** ensures desired state.  

## Cleanup
```bash
microk8s kubectl delete pod arch-demo
```

## Testing Checklist
- [ ] Cluster status shows all services running.  
- [ ] Nodes listed and described successfully.  
- [ ] Control plane Pods visible in `kube-system`.  
- [ ] Worker node services verified.  
- [ ] Test Pod deployed and deleted successfully.  


## Key Learning
- Control plane manages cluster state.  
- Worker nodes run workloads.  
- System Pods in `kube-system` namespace represent core services.  
- Kubernetes architecture ensures declarative state management.  


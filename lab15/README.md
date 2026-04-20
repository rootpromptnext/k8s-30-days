# Lab 15: Services (ClusterIP, NodePort, LoadBalancer)

## Objective
Understand how Kubernetes **Services** expose Pods:
- **ClusterIP** → internal access only.  
- **NodePort** → external access via node IP + port.  
- **LoadBalancer** → cloud‑integrated external access.  

---

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Deployments and Pods.  

---

## Step 1: Create Deployment
```bash
microk8s kubectl create deployment svc-demo --image=nginx
microk8s kubectl scale deployment svc-demo --replicas=2
microk8s kubectl get pods
```

---

## Step 2: ClusterIP Service (Default)
```bash
microk8s kubectl expose deployment svc-demo --port=80 --target-port=80 --name=svc-clusterip
microk8s kubectl get svc svc-clusterip
```

Test internal access (exec into Pod):
```bash
microk8s kubectl exec -it <pod-name> -- curl svc-clusterip
```
Works inside cluster only.

---

## Step 3: NodePort Service
```bash
microk8s kubectl expose deployment svc-demo --port=80 --type=NodePort --name=svc-nodeport
microk8s kubectl get svc svc-nodeport
```

Test external access:
```bash
curl http://localhost:<NodePort>
```
Accessible from outside cluster via node IP + port.

---

## Step 4: LoadBalancer Service (Conceptual in MicroK8s)
```bash
microk8s kubectl expose deployment svc-demo --port=80 --type=LoadBalancer --name=svc-loadbalancer
microk8s kubectl get svc svc-loadbalancer
```

- In **cloud environments (EKS/GKE/AKS)**, this provisions a cloud load balancer.  
- In **MicroK8s**, it behaves like NodePort unless integrated with MetalLB add‑on.  

Enable MetalLB (optional):
```bash
microk8s enable metallb:192.168.1.240-192.168.1.250
```
Assigns external IPs for LoadBalancer services.

---

## Step 5: Clean Up
```bash
microk8s kubectl delete svc svc-clusterip svc-nodeport svc-loadbalancer
microk8s kubectl delete deployment svc-demo
```

---

## Testing Checklist
- [ ] ClusterIP Service created and accessible internally.  
- [ ] NodePort Service accessible externally via node IP + port.  
- [ ] LoadBalancer Service created (conceptual in MicroK8s, real in cloud).  
- [ ] Resources cleaned up.  

---

## Key Learning
- **ClusterIP** → default, internal cluster communication.  
- **NodePort** → exposes service externally via node IP + port.  
- **LoadBalancer** → integrates with cloud provider to provision external load balancer.  
- MicroK8s can simulate LoadBalancer with **MetalLB add‑on**.  


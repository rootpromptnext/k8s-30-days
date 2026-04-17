# Lab 4: Containers vs Virtual Machines Demo

## Objective
Understand the difference between **containers** and **virtual machines (VMs)** by running a simple demo:
- Launch a VM and check its resource footprint.  
- Launch a container and compare.  
- Observe why Kubernetes builds on containers, not VMs.  


## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Docker installed (for container demo).  
- VirtualBox or Multipass installed (for VM demo).  

## Step 1: Run a Virtual Machine
Using Multipass (lightweight VM manager):
```bash
multipass launch --name vm-demo --mem 1G --disk 5G
multipass list
```

Check VM resource usage:
```bash
multipass exec vm-demo -- free -m
multipass exec vm-demo -- df -h
```

Stop and delete VM:
```bash
multipass delete vm-demo
multipass purge
```

## Step 2: Run a Container
Run NGINX container with Docker:
```bash
docker run -d --name container-demo -p 8080:80 nginx
```

Check container resource usage:
```bash
docker stats container-demo
```

Test container:
```bash
curl http://localhost:8080
```

Stop and remove container:
```bash
docker rm -f container-demo
```

## Step 3: Compare Containers vs VMs
- **VMs**: Heavy, emulate hardware, slower startup.  
- **Containers**: Lightweight, share host kernel, faster startup.  
- Kubernetes leverages containers for efficiency and scalability.  

## Step 4: Explore Kubernetes Alternatives
For awareness (no lab execution here):
- **Docker Swarm**: Simpler orchestration.  
- **Mesos/Marathon**: Older cluster manager.  
- **Nomad**: HashiCorp’s scheduler.  

## Step 5: Clean Up
Ensure no leftover resources:
```bash
multipass list   # should be empty
docker ps -a     # no container-demo
```

## Testing Checklist
- [ ] VM launched and inspected with Multipass.  
- [ ] Container launched and inspected with Docker.  
- [ ] Resource usage compared.  
- [ ] VM and container cleaned up.  

## Key Learning
- VMs virtualize hardware; containers virtualize the OS.  
- Containers are lightweight, portable, and fast.  
- Kubernetes builds on containers for orchestration.  
- Alternatives exist, but Kubernetes dominates due to ecosystem maturity.  


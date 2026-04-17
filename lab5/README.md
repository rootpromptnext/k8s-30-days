# Lab 5: Docker Basics Before Kubernetes

## Objective
Learn the basics of Docker, the container runtime that Kubernetes builds upon:
- Install Docker.  
- Build and run a container.  
- Inspect container lifecycle.  
- Test container networking.  
- Clean up resources.  


## Prerequisites
- Linux system (Ubuntu recommended).  
- MicroK8s installed (for later labs).  
- Internet connection.  


## Step 1: Install Docker
```bash
sudo apt update
sudo apt install docker.io -y
```

Enable and start Docker:
```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Verify installation:
```bash
docker --version
```
## Step 2: Run Your First Container
Pull and run NGINX:
```bash
docker run -d --name docker-nginx -p 8080:80 nginx
```

Check running containers:
```bash
docker ps
```


## Step 3: Inspect Container
View logs:
```bash
docker logs docker-nginx
```

Inspect container details:
```bash
docker inspect docker-nginx
```

Check resource usage:
```bash
docker stats docker-nginx
```

## Step 4: Test Networking
Access container via port mapping:
```bash
curl http://localhost:8080
```
Expected: NGINX welcome page HTML.

## Step 5: Clean Up
Stop and remove container:
```bash
docker rm -f docker-nginx
```

Remove image:
```bash
docker rmi nginx
```

Check cleanup:
```bash
docker ps -a
docker images
```

## Testing Checklist
- [ ] Docker installed and running.  
- [ ] NGINX container deployed successfully.  
- [ ] Logs and inspect output accessible.  
- [ ] Container responds via port 8080.  
- [ ] Container and image cleaned up.  


## Key Learning
- Docker runs containers directly on the host OS.  
- Containers are lightweight and portable.  
- Kubernetes builds on Docker (or other runtimes) to orchestrate containers at scale.  
- Understanding Docker basics is essential before learning Kubernetes.  


# Lab 16: Ingress & External Access

## Objective
Learn how to expose applications externally using **Ingress**:
- Enable Ingress controller in MicroK8s.  
- Create a Deployment and Service.  
- Define an Ingress resource for HTTP routing.  
- Test external access.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Services (ClusterIP, NodePort, LoadBalancer).  

## Step 1: Enable Ingress in MicroK8s
```bash
microk8s enable ingress
```

Verify controller:
```bash
microk8s kubectl get pods -n ingress
```

## Step 2: Create Deployment & Service
```bash
microk8s kubectl create deployment ingress-demo --image=nginx
microk8s kubectl expose deployment ingress-demo --port=80 --target-port=80 --name=ingress-demo-svc
microk8s kubectl get svc ingress-demo-svc
```

## Step 3: Create Ingress Resource
Create a file named `ingress-demo.yaml`:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-demo
spec:
  rules:
  - host: demo.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: ingress-demo-svc
            port:
              number: 80
```

Apply it:
```bash
microk8s kubectl apply -f ingress-demo.yaml
microk8s kubectl get ingress
```

## Step 4: Test External Access
Update `/etc/hosts` to map `demo.local` to `127.0.0.1`:
```bash
sudo echo "127.0.0.1 demo.local" >> /etc/hosts
```

Test access:
```bash
curl http://demo.local
```
Expected: NGINX welcome page HTML.

## Step 5: Clean Up
```bash
microk8s kubectl delete ingress ingress-demo
microk8s kubectl delete svc ingress-demo-svc
microk8s kubectl delete deployment ingress-demo
```

## Testing Checklist
- [ ] Ingress controller enabled.  
- [ ] Deployment and Service created.  
- [ ] Ingress resource applied successfully.  
- [ ] External access tested via custom host.  
- [ ] Resources cleaned up.  

## Key Learning
- **Ingress** provides HTTP routing rules for external access.  
- Unlike NodePort/LoadBalancer, Ingress allows **path‑based and host‑based routing**.  
- In MicroK8s, Ingress requires enabling the built‑in controller.  
- In production, Ingress integrates with cloud load balancers and reverse proxies.  


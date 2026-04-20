# Lab 28: CI/CD, GitOps & Helm

## Objective
Learn how Kubernetes integrates with modern delivery pipelines:
- Set up a simple CI/CD pipeline.  
- Understand GitOps workflow.  
- Use Helm for package management. 

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Deployments and Services.  
- GitHub account (for GitOps demo).  
- Helm installed locally:
```bash
sudo snap install helm --classic
```

## Step 1: CI/CD Pipeline Basics
CI/CD automates build, test, and deploy.

Example GitHub Actions workflow `.github/workflows/deploy.yaml`:
```yaml
name: CI/CD Pipeline
on:
  push:
    branches: [ "main" ]
jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    - name: Build Docker image
      run: docker build -t myapp:latest .
    - name: Push to registry
      run: docker push myregistry/myapp:latest
    - name: Deploy to Kubernetes
      run: |
        microk8s kubectl set image deployment/myapp myapp=myregistry/myapp:latest
```

Every commit to `main` triggers build + deploy.


## Step 2: GitOps Workflow
GitOps = **declarative infrastructure + version control + automation**.

- Store Kubernetes manifests in Git.  
- Use tools like **ArgoCD** or **Flux** to sync cluster state with Git repo.  
- Any change in Git automatically applies to cluster.  

Example ArgoCD Application manifest:
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
spec:
  source:
    repoURL: https://github.com/user/myapp-configs.git
    path: manifests
    targetRevision: main
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Git is the **single source of truth**.

## Step 3: Helm Basics
Helm = Kubernetes package manager.

Create Helm chart:
```bash
helm create mychart
```

Install chart:
```bash
helm install myapp mychart/
```

Upgrade release:
```bash
helm upgrade myapp mychart/
```

List releases:
```bash
helm list
```

Helm simplifies deploying complex apps (Prometheus, Grafana, EFK).

## Step 4: Clean Up
```bash
helm uninstall myapp
microk8s kubectl delete deployment myapp
```

## Testing Checklist
- [ ] CI/CD pipeline defined in GitHub Actions.  
- [ ] GitOps repo synced with ArgoCD/Flux.  
- [ ] Helm chart created and deployed.  
- [ ] Helm release upgraded and listed.  
- [ ] Resources cleaned up.  


## Key Learning
- **CI/CD** automates build, test, deploy pipelines.  
- **GitOps** ensures declarative, version‑controlled cluster state.  
- **Helm** packages Kubernetes apps for easy install/upgrade.  
- Together, they enable **modern, production‑ready delivery workflows**.  

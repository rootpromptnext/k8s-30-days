# Lab 14: Workload Comparison (Deployments vs StatefulSets vs Jobs vs CronJobs)

## Objective
Compare the main Kubernetes workload types:
- **Deployment** → stateless apps with scaling and rolling updates.  
- **StatefulSet** → stateful apps with stable identity and ordered Pods.  
- **Job** → one‑time batch tasks.  
- **CronJob** → scheduled recurring tasks.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Completed Labs 8–13 for context.  

## Step 1: Create Deployment (Stateless)
```bash
microk8s kubectl create deployment demo-deploy --image=nginx
microk8s kubectl scale deployment demo-deploy --replicas=3
microk8s kubectl get pods
```
Pods are interchangeable, no stable identity.

## Step 2: Create StatefulSet (Stateful)
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: demo-sts
spec:
  serviceName: "demo-headless"
  replicas: 2
  selector:
    matchLabels:
      app: demo-sts
  template:
    metadata:
      labels:
        app: demo-sts
    spec:
      containers:
      - name: nginx
        image: nginx
```

Apply:
```bash
microk8s kubectl apply -f demo-sts.yaml
microk8s kubectl get pods -l app=demo-sts
```
Pods named `demo-sts-0`, `demo-sts-1` with stable DNS.

## Step 3: Create Job (Batch Task)
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: demo-job
spec:
  template:
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ["echo", "Hello from Job!"]
      restartPolicy: Never
```

Apply:
```bash
microk8s kubectl apply -f demo-job.yaml
microk8s kubectl logs <job-pod>
```

## Step 4: Create CronJob (Scheduled Task)
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: demo-cron
spec:
  schedule: "*/2 * * * *"   # every 2 minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: busybox
            image: busybox
            command: ["echo", "Hello from CronJob!"]
          restartPolicy: Never
```

Apply:
```bash
microk8s kubectl apply -f demo-cron.yaml
microk8s kubectl get cronjobs
```

## Step 5: Clean Up
```bash
microk8s kubectl delete deployment demo-deploy
microk8s kubectl delete statefulset demo-sts
microk8s kubectl delete job demo-job
microk8s kubectl delete cronjob demo-cron
```


## Testing Checklist
- [ ] Deployment created and scaled successfully.  
- [ ] StatefulSet created with stable Pod names.  
- [ ] Job executed once and completed.  
- [ ] CronJob scheduled Jobs automatically.  
- [ ] All resources cleaned up.  



## Key Learning
- **Deployment** → stateless, scalable, rolling updates.  
- **StatefulSet** → stateful, stable identity, ordered Pods.  
- **Job** → one‑time batch execution.  
- **CronJob** → recurring scheduled execution.  
- Choosing the right workload type depends on the application’s requirements.  


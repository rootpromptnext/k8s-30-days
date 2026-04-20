# Lab 13: Jobs & CronJobs

## Objective
Learn how Kubernetes handles **one‑time tasks** and **scheduled tasks**:
- Create a Job to run a batch task.  
- Observe Pod lifecycle.  
- Create a CronJob for recurring tasks.  
- Inspect scheduling and cleanup.  


## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods and Deployments.  


## Step 1: Create a Job
Create a file named `job-demo.yaml`:
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello-job
spec:
  template:
    spec:
      containers:
      - name: hello
        image: busybox
        command: ["echo", "Hello from Kubernetes Job!"]
      restartPolicy: Never
```

Apply it:
```bash
microk8s kubectl apply -f job-demo.yaml
microk8s kubectl get jobs
microk8s kubectl get pods
```

Check logs:
```bash
microk8s kubectl logs <pod-name>
```
Expected: `Hello from Kubernetes Job!`

## Step 2: Observe Job Lifecycle
Jobs run Pods until completion.  
Check status:
```bash
microk8s kubectl describe job hello-job
```
You’ll see `Succeeded: 1`.

## Step 3: Create a CronJob
Create a file named `cronjob-demo.yaml`:
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cron
spec:
  schedule: "*/1 * * * *"   # every minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command: ["echo", "Hello from Kubernetes CronJob!"]
          restartPolicy: Never
```

Apply it:
```bash
microk8s kubectl apply -f cronjob-demo.yaml
microk8s kubectl get cronjobs
```

## Step 4: Inspect CronJob Execution
Check Jobs created by CronJob:
```bash
microk8s kubectl get jobs
```

Check Pods:
```bash
microk8s kubectl get pods
```

View logs:
```bash
microk8s kubectl logs <pod-name>
```
Expected: `Hello from Kubernetes CronJob!`

## Step 5: Clean Up
```bash
microk8s kubectl delete cronjob hello-cron
microk8s kubectl delete job hello-job
```

## Testing Checklist
- [ ] Job created and executed successfully.  
- [ ] Job Pod logs show expected output.  
- [ ] CronJob created and scheduled Jobs automatically.  
- [ ] CronJob Pods executed on schedule.  
- [ ] Resources cleaned up.  


## Key Learning
- **Jobs** run Pods until completion — ideal for batch tasks.  
- **CronJobs** schedule Jobs at fixed intervals — ideal for recurring tasks.  
- Kubernetes ensures reliability of batch workloads.  
- These constructs complement Deployments and StatefulSets for non‑continuous workloads.  

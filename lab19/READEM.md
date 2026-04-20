# Lab 19: Secrets Usage

## Objective
Learn how to use **Secrets** in Kubernetes:
- Create a Secret.  
- Inject it into Pods as environment variables.  
- Mount it as a volume.  
- Test and clean up.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with ConfigMaps (Lab 18).  

## Step 1: Create Secret
Create a Secret from literal values:
```bash
microk8s kubectl create secret generic app-secret \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASS=Pa55w0rd
```

Check Secret:
```bash
microk8s kubectl get secrets
microk8s kubectl describe secret app-secret
```

Values are stored **base64‑encoded**.

## Step 2: Use Secret as Environment Variables
Create a Pod manifest `pod-secret-env.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-secret-env
spec:
  containers:
  - name: demo-container
    image: busybox
    command: ["sh", "-c", "echo User=$DB_USER Pass=$DB_PASS && sleep 3600"]
    envFrom:
    - secretRef:
        name: app-secret
```

Apply it:
```bash
microk8s kubectl apply -f pod-secret-env.yaml
microk8s kubectl logs pod-secret-env
```
Expected: `User=admin Pass=Pa55w0rd`

## Step 3: Use Secret as Volume
Create a Pod manifest `pod-secret-volume.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-secret-volume
spec:
  containers:
  - name: demo-container
    image: busybox
    command: ["sh", "-c", "cat /secrets/DB_USER && cat /secrets/DB_PASS && sleep 3600"]
    volumeMounts:
    - name: secret-volume
      mountPath: /secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: app-secret
```

Apply it:
```bash
microk8s kubectl apply -f pod-secret-volume.yaml
microk8s kubectl logs pod-secret-volume
```
Expected:  
```
admin
Pa55w0rd
```

## Step 4: Clean Up
```bash
microk8s kubectl delete pod pod-secret-env pod-secret-volume
microk8s kubectl delete secret app-secret
```

## Testing Checklist
- [ ] Secret created successfully.  
- [ ] Pod consumed Secret as environment variables.  
- [ ] Pod consumed Secret as mounted files.  
- [ ] Logs show expected sensitive values.  
- [ ] Resources cleaned up.  

## Key Learning
- **Secrets** store sensitive data (passwords, tokens, keys) securely.  
- They can be injected into Pods as **environment variables** or **mounted files**.  
- Unlike ConfigMaps, Secrets are base64‑encoded and treated with stricter access controls.  
- Best practice: never hardcode sensitive values in Pod manifests — use Secrets instead.  


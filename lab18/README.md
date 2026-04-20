# Lab 18: ConfigMaps Usage

## Objective
Learn how to use **ConfigMaps** to inject configuration into Pods:
- Create a ConfigMap.  
- Mount it as environment variables.  
- Mount it as a volume.  
- Test and clean up.  


## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods and YAML manifests.  


## Step 1: Create ConfigMap
Create a file named `app-config.yaml`:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MODE: "development"
  APP_COLOR: "blue"
```

Apply it:
```bash
microk8s kubectl apply -f app-config.yaml
microk8s kubectl get configmaps
microk8s kubectl describe configmap app-config
```

## Step 2: Use ConfigMap as Environment Variables
Create a Pod manifest `pod-env.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-env-demo
spec:
  containers:
  - name: demo-container
    image: busybox
    command: ["sh", "-c", "echo Mode=$APP_MODE Color=$APP_COLOR && sleep 3600"]
    envFrom:
    - configMapRef:
        name: app-config
```

Apply it:
```bash
microk8s kubectl apply -f pod-env.yaml
microk8s kubectl logs pod-env-demo
```
Expected: `Mode=development Color=blue`

## Step 3: Use ConfigMap as Volume
Create a Pod manifest `pod-volume.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-volume-demo
spec:
  containers:
  - name: demo-container
    image: busybox
    command: ["sh", "-c", "cat /config/APP_MODE && cat /config/APP_COLOR && sleep 3600"]
    volumeMounts:
    - name: config-volume
      mountPath: /config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
```

Apply it:
```bash
microk8s kubectl apply -f pod-volume.yaml
microk8s kubectl logs pod-volume-demo
```

Expected:  
```
development
blue
```

## Step 4: Clean Up
```bash
microk8s kubectl delete pod pod-env-demo pod-volume-demo
microk8s kubectl delete configmap app-config
```

## Testing Checklist
- [ ] ConfigMap created successfully.  
- [ ] Pod consumed ConfigMap as environment variables.  
- [ ] Pod consumed ConfigMap as mounted files.  
- [ ] Logs show expected values.  
- [ ] Resources cleaned up.  

## Key Learning
- **ConfigMaps** decouple configuration from application code.  
- They can be injected into Pods as **environment variables** or **mounted files**.  
- Useful for managing environment‑specific settings without rebuilding images.  
- ConfigMaps complement **Secrets** (for sensitive data).  


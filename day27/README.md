# Lab 27: Storage (PV, PVC, CSI)

## Objective
Learn how Kubernetes manages persistent storage:
- Create a **PersistentVolume (PV)**.  
- Bind it with a **PersistentVolumeClaim (PVC)**.  
- Use PVC in a Pod.  
- Understand CSI drivers for dynamic provisioning.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Pods and YAML manifests.  
- Optional: MicroK8s `hostpath-storage` add‑on enabled for local PVs:
```bash
microk8s enable hostpath-storage
```

## Step 1: Create PersistentVolume
Create `pv.yaml`:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: demo-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/demo-pv
```

Apply:
```bash
microk8s kubectl apply -f pv.yaml
microk8s kubectl get pv
```

## Step 2: Create PersistentVolumeClaim
Create `pvc.yaml`:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: demo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

Apply:
```bash
microk8s kubectl apply -f pvc.yaml
microk8s kubectl get pvc
```

PVC should bind to PV if requirements match.

---

## Step 3: Use PVC in a Pod
Create `pod-pvc.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-pvc-demo
spec:
  containers:
  - name: demo-container
    image: busybox
    command: ["sh", "-c", "echo 'Hello PV' > /mnt/data/hello.txt && sleep 3600"]
    volumeMounts:
    - mountPath: /mnt/data
      name: demo-storage
  volumes:
  - name: demo-storage
    persistentVolumeClaim:
      claimName: demo-pvc
```

Apply:
```bash
microk8s kubectl apply -f pod-pvc.yaml
microk8s kubectl exec pod-pvc-demo -- cat /mnt/data/hello.txt
```
Expected output: `Hello PV`

## Step 4: CSI Drivers (Conceptual)
- **CSI (Container Storage Interface)** standardizes storage plugins.  
- Cloud providers (AWS EBS, GCP PD, Azure Disk) use CSI drivers for dynamic provisioning.  
- In MicroK8s, `hostpath-storage` acts as a simple CSI driver for local storage.  
- Dynamic provisioning: PVC automatically triggers PV creation via CSI driver.


## Step 5: Clean Up
```bash
microk8s kubectl delete pod pod-pvc-demo
microk8s kubectl delete pvc demo-pvc
microk8s kubectl delete pv demo-pv
```

## Testing Checklist
- [ ] PV created successfully.  
- [ ] PVC bound to PV.  
- [ ] Pod mounted PVC and wrote data.  
- [ ] Data persisted in volume.  
- [ ] Resources cleaned up.  


## Key Learning
- **PersistentVolumes (PV)** represent cluster storage resources.  
- **PersistentVolumeClaims (PVC)** request storage from PVs.  
- Pods use PVCs to mount persistent storage.  
- **CSI drivers** enable dynamic provisioning and cloud integration.  
- Storage is critical for stateful workloads (databases, apps needing durability).  

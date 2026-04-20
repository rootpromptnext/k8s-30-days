# Lab 29: CRDs & Custom Controllers

## Objective
Learn how Kubernetes can be extended beyond built‑in resources:
- Define a **Custom Resource Definition (CRD)**.  
- Create and manage custom resources.  
- Write a simple custom controller.  
- Observe reconciliation loop behavior.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with Deployments, Pods, and RBAC.  
- Basic knowledge of YAML and Go/Python (for controllers).  

## Step 1: Create a Custom Resource Definition
Create `crd-demo.yaml`:
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: demos.example.com
spec:
  group: example.com
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              message:
                type: string
  scope: Namespaced
  names:
    plural: demos
    singular: demo
    kind: Demo
    shortNames:
    - dm
```

Apply:
```bash
microk8s kubectl apply -f crd-demo.yaml
microk8s kubectl get crd
```

## Step 2: Create a Custom Resource
Create `demo-resource.yaml`:
```yaml
apiVersion: example.com/v1
kind: Demo
metadata:
  name: hello-demo
spec:
  message: "Hello from CRD!"
```

Apply:
```bash
microk8s kubectl apply -f demo-resource.yaml
microk8s kubectl get demos
microk8s kubectl describe demo hello-demo
```

Custom resource behaves like native Kubernetes objects.

## Step 3: Write a Simple Controller
Controllers watch resources and reconcile desired state.

Example Python controller (using `client-python`):
```python
from kubernetes import client, config, watch

config.load_kube_config()
api = client.CustomObjectsApi()
w = watch.Watch()

for event in w.stream(api.list_namespaced_custom_object,
                      group="example.com", version="v1",
                      namespace="default", plural="demos"):
    obj = event['object']
    name = obj['metadata']['name']
    message = obj['spec'].get('message', '')
    print(f"Observed Demo {name}: {message}")
```

This controller prints the `message` field whenever a Demo resource is created/updated.

## Step 4: Test Controller Behavior
Run the controller:
```bash
python demo-controller.py
```

Update resource:
```bash
microk8s kubectl edit demo hello-demo
```

Change message to `"Updated message!"`.

Controller prints new message automatically.


## Step 5: Clean Up
```bash
microk8s kubectl delete demo hello-demo
microk8s kubectl delete crd demos.example.com
```

## Testing Checklist
- [ ] CRD created successfully.  
- [ ] Custom resource applied and observed.  
- [ ] Controller detected changes.  
- [ ] Reconciliation loop worked.  
- [ ] Resources cleaned up.  


## Key Learning
- **CRDs** extend Kubernetes API with new resource types.  
- **Custom controllers** implement reconciliation loops for those resources.  
- Together, they enable **Operators** — Kubernetes‑native automation for complex apps.  
- This pattern is widely used for databases, monitoring stacks, and cloud integrations.  


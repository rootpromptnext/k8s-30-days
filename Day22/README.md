# Lab 22: RBAC Roles & Bindings

## Objective
Learn how Kubernetes secures cluster resources with RBAC:
- Create a Role with restricted permissions.  
- Bind the Role to a user/service account.  
- Test access with and without binding.  
- Clean up resources.  

## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Familiarity with namespaces and basic `kubectl` commands.  
- RBAC enabled (default in MicroK8s).  

## Step 1: Create Namespace & Service Account
```bash
microk8s kubectl create namespace rbac-demo
microk8s kubectl create serviceaccount demo-user -n rbac-demo
microk8s kubectl get sa -n rbac-demo
```

## Step 2: Create Role
Create a file named `role.yaml`:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: rbac-demo
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

Apply it:
```bash
microk8s kubectl apply -f role.yaml
microk8s kubectl get role -n rbac-demo
```

## Step 3: Create RoleBinding
Create a file named `rolebinding.yaml`:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: rbac-demo
subjects:
- kind: ServiceAccount
  name: demo-user
  namespace: rbac-demo
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

Apply it:
```bash
microk8s kubectl apply -f rolebinding.yaml
microk8s kubectl get rolebinding -n rbac-demo
```

## Step 4: Test Access
Deploy a Pod in `rbac-demo` namespace:
```bash
microk8s kubectl run nginx --image=nginx -n rbac-demo
```

Try accessing Pods with the service account:
```bash
kubectl auth can-i list pods --as=system:serviceaccount:rbac-demo:demo-user -n rbac-demo
```
Expected: `yes`

Try accessing ConfigMaps:
```bash
kubectl auth can-i list configmaps --as=system:serviceaccount:rbac-demo:demo-user -n rbac-demo
```
Expected: `no` (not allowed).

## Step 5: Clean Up
```bash
microk8s kubectl delete ns rbac-demo
```

## Testing Checklist
- [ ] Namespace and service account created.  
- [ ] Role created with restricted permissions.  
- [ ] RoleBinding applied successfully.  
- [ ] Access tested with allowed and denied actions.  
- [ ] Resources cleaned up.  

## Key Learning
- **Roles** define permissions within a namespace.  
- **RoleBindings** attach Roles to users/service accounts.  
- RBAC enforces **least privilege** principle.  
- Cluster‑wide permissions use **ClusterRoles** and **ClusterRoleBindings**.  

# Lab 26: Scheduling — Taints, Tolerations, and Priorities

## Objectives
- Understand how Kubernetes schedules Pods using taints and tolerations.
- Learn how to prevent Pods from running on certain nodes.
- Explore Pod priority and preemption to control scheduling decisions.

## Prerequisites
- MicroK8s or any Kubernetes cluster running.
- At least 2 worker nodes (to demonstrate scheduling behavior).
- `kubectl` configured.

## Step 1: Apply a Taint to a Node
Taints mark nodes as unsuitable for certain Pods unless they tolerate the taint.

```bash
# Taint a node to repel Pods without toleration
kubectl taint nodes <node-name> key1=value1:NoSchedule
```

Verify:
```bash
kubectl describe node <node-name> | grep Taints
```

## Step 2: Create a Pod Without Toleration
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: no-toleration-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply:
```bash
kubectl apply -f no-toleration-pod.yaml
kubectl get pods -o wide
```

Pod will remain **Pending** because it cannot tolerate the taint.

---

## Step 3: Create a Pod With Toleration
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  tolerations:
  - key: "key1"
    operator: "Equal"
    value: "value1"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

Apply:
```bash
kubectl apply -f toleration-pod.yaml
kubectl get pods -o wide
```

This Pod will successfully schedule on the tainted node.

## Step 4: Pod Priority and Preemption
Define a **PriorityClass**:

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
description: "High priority class"
```

Apply:
```bash
kubectl apply -f priorityclass.yaml
```

Create a Pod with this priority:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: high-priority-pod
spec:
  priorityClassName: high-priority
  containers:
  - name: nginx
    image: nginx
```

If resources are scarce, this Pod may **preempt** lower-priority Pods.

## Cleanup
```bash
kubectl delete pod no-toleration-pod toleration-pod high-priority-pod
kubectl delete priorityclass high-priority
kubectl taint nodes <node-name> key1=value1:NoSchedule-
```

## Key Learnings
- **Taints** repel Pods from nodes.
- **Tolerations** allow Pods to run on tainted nodes.
- **PriorityClasses** influence scheduling decisions and can preempt lower-priority Pods.
- Together, these mechanisms give fine-grained control over workload placement.


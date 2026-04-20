# Lab 24: Monitoring & Logging Stack

## Objective
Learn how to set up a **monitoring and logging stack** in Kubernetes:
- Enable built‑in add‑ons for metrics and dashboards.  
- Deploy Prometheus & Grafana for monitoring.  
- Deploy Elasticsearch, Fluentd, and Kibana (EFK) for logging.  
- Explore metrics and logs.  


## Prerequisites
- MicroK8s installed (`microk8s status --wait-ready`).  
- Metrics server enabled (Lab 21).  
- Familiarity with Pods, Services, and Ingress.  


## Step 1: Enable Monitoring Add‑ons
MicroK8s provides Prometheus and Grafana:
```bash
microk8s enable prometheus
microk8s enable grafana
```

Check pods:
```bash
microk8s kubectl get pods -n monitoring
```

Prometheus scrapes metrics, Grafana visualizes them.

## Step 2: Access Grafana Dashboard
Expose Grafana:
```bash
microk8s kubectl port-forward -n monitoring svc/grafana 3000:3000
```

Open browser:
```
http://localhost:3000
```

Default credentials:
- User: `admin`  
- Password: `admin`  

Explore dashboards for CPU, memory, and workload metrics.

## Step 3: Deploy Logging Stack (EFK)
Enable EFK in MicroK8s:
```bash
microk8s enable elasticsearch
microk8s enable fluentd
microk8s enable kibana
```

Check pods:
```bash
microk8s kubectl get pods -n logging
```

Fluentd collects logs, Elasticsearch stores them, Kibana visualizes them.

## Step 4: Access Kibana
Expose Kibana:
```bash
microk8s kubectl port-forward -n logging svc/kibana 5601:5601
```

Open browser:
```
http://localhost:5601
```

Explore logs from Pods and Services.

## Step 5: Generate Logs & Metrics
Deploy a sample app:
```bash
microk8s kubectl create deployment log-demo --image=nginx
microk8s kubectl expose deployment log-demo --port=80 --type=NodePort
```

Access app:
```bash
curl http://localhost:<NodePort>
```

Check logs in Kibana and metrics in Grafana.


## Step 6: Clean Up
```bash
microk8s kubectl delete deployment log-demo
microk8s kubectl delete svc log-demo
```

(Optional) disable add‑ons:
```bash
microk8s disable prometheus grafana elasticsearch fluentd kibana
```


## Testing Checklist
- [ ] Prometheus & Grafana enabled and accessible.  
- [ ] EFK stack enabled and accessible.  
- [ ] Sample app deployed and logs visible in Kibana.  
- [ ] Metrics visible in Grafana dashboards.  
- [ ] Resources cleaned up.  


## Key Learning
- **Prometheus** scrapes metrics, **Grafana** visualizes them.  
- **Fluentd** collects logs, **Elasticsearch** stores them, **Kibana** visualizes them.  
- Monitoring + logging = **observability stack** for Kubernetes.  
- Essential for debugging, performance tuning, and production readiness.  


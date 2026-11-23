# Ingress Controller Deployment Manifests

## 📌 Overview
This directory contains deployment manifests for the **NGINX Ingress Controller** adapted for different hosting infrastructures.

---

## 📄 Manifest Files in this Directory

| Manifest File | Environment / Type | Description |
|---|---|---|
| `ingress-controller-kind-deploy.yaml` | **Kind (Kubernetes in Docker)** | Optimized for local Kind clusters with hostPort mappings. |
| `ingress-controller-loadbalancer-deploy.yaml` | **Cloud Load Balancer** | Uses `type: LoadBalancer` to provision cloud provider LBs (AWS ELB, GCP LB). |
| `ingress-controller-nodeport-deploy.yaml` | **Bare-metal / On-Prem** | Exposes the ingress controller via a static NodePort service (`30000-32767`). |
| `ingress-controller-nodeport-azure-deploy.yaml` | **Azure AKS / VM** | Ingress controller customized for Azure Kubernetes Service node configurations. |

---

## 🛠️ Quick Deployment Commands
```bash
# Deploy bare-metal NodePort ingress controller
kubectl apply -f ingress-controller-nodeport-deploy.yaml

# Verify controller pods and ingress-nginx namespace
kubectl get pods -n ingress-nginx -o wide
kubectl get svc -n ingress-nginx
```

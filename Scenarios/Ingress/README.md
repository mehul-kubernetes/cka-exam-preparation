# Kubernetes Ingress

## 📌 Overview
An **Ingress** exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.

An Ingress requires two components:
1. **Ingress Controller**: The reverse proxy / load balancer daemon (e.g. `ingress-nginx`).
2. **Ingress Resource**: The declarative routing rules (`kind: Ingress`).

---

## 📂 Subdirectories

- [`nginx/`](./nginx/README.md): NGINX Ingress Controller installation manifests (Baremetal NodePort, Kind, Cloud LoadBalancer, Azure) and sample backend applications.

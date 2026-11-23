# NGINX Ingress Controller & Routing Scenarios

## 📌 Overview
This directory contains provider-specific deployment manifests for the **NGINX Ingress Controller** and sample microservices demonstrating path-based and host-based routing.

---

## 📂 Subdirectories

| Directory | Content |
|---|---|
| [`controller/`](./controller/README.md) | Ingress Controller manifests for Kind, Cloud LoadBalancer, Bare-metal NodePort, and Azure. |
| [`sourcefiles/`](./sourcefiles/README.md) | Sample backend applications (`hello1`, `hello2`, `webapp1`, `webapp2`) to test ingress routing. |

---

## 🔍 Verification & URL Testing
```bash
# Testing host-based routing with curl:
curl <worker-external-ip>:<node-port> -H 'Host: webapp.myexample.co.in'

# Testing DNS resolution resolution inline:
curl --resolve "webapp.myexample.co.in:80:<ingress-ip>" -i http://webapp.myexample.co.in
```

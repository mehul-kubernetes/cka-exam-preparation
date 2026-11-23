# Sample Applications for Ingress Routing

## 📌 Overview
This directory contains sample backend deployments, services, and Ingress resources demonstrating **host-based** and **path-based** HTTP routing.

---

## 📄 Manifest Files in this Directory

| File | Type | Hostname / Path | Backend Service |
|---|---|---|---|
| `hello1.yaml` | Ingress | `hello.myexample.co.in/` | `my-dep:80` |
| `hello2.yaml` | Ingress | `hello.myexample.co.in/` (extended) | `my-dep:80` |
| `webapp1.yaml` | Ingress | `webapp.myexample.co.in/` | `my-svc:80` |
| `webapp2.yaml` | Ingress | `webapp.myexample.co.in/` (extended) | `my-svc:80` |

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy Sample Backends**:
   ```bash
   kubectl create deployment my-dep --image=nginx --replicas=2
   kubectl expose deployment my-dep --port=80

   kubectl create deployment my-svc --image=httpd:alpine --replicas=2
   kubectl expose deployment my-svc --port=80
   ```

2. **Apply Ingress Manifests**:
   ```bash
   kubectl apply -f hello1.yaml
   kubectl apply -f webapp1.yaml
   ```

3. **Verify Ingress Creation**:
   ```bash
   kubectl get ingress
   kubectl describe ingress webapp
   ```

4. **Test Traffic with HTTP Host Header**:
   ```bash
   curl <INGRESS_IP_OR_NODE_IP>:<PORT> -H 'Host: hello.myexample.co.in'
   curl <INGRESS_IP_OR_NODE_IP>:<PORT> -H 'Host: webapp.myexample.co.in'
   ```

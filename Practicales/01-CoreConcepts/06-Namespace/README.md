# 06 - Namespaces

## 📌 Overview
**Namespaces** provide a mechanism for isolating groups of resources within a single Kubernetes cluster. Names of resources must be unique within a namespace, but not across namespaces.

---

## 🚀 CKA Fast Imperative Commands

```bash
# Create a namespace
kubectl create namespace dev
kubectl create ns prod

# Generate namespace manifest
kubectl create ns test --dry-run=client -o yaml > namespace.yaml

# List resources in a specific namespace
kubectl get pods -n dev
kubectl get all -n prod

# List resources across ALL namespaces
kubectl get pods -A
# or
kubectl get pods --all-namespaces

# Permanently switch default namespace for your kubectl context (Exam Game Changer!)
kubectl config set-context --current --namespace=dev

# Verify current active namespace
kubectl config view --minify | grep namespace:
```

---

## 📄 Manifest Example: `namespace.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: finance
  labels:
    tier: enterprise
```

### Pod in a Specific Namespace
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
  namespace: finance
spec:
  containers:
  - name: secure-app
    image: nginx
```

---

## 🌐 Cross-Namespace Communication & DNS
Resources in different namespaces communicate via FQDN (Fully Qualified Domain Name):
```
<service-name>.<namespace>.svc.cluster.local
```
Example: Accessing `auth-svc` in `finance` namespace from `dev` namespace:
```bash
curl http://auth-svc.finance.svc.cluster.local:8080
```

---

## 💡 CKA Exam Tips
- Exam questions frequently start with: *"In namespace `payroll`, perform..."*.
- Always verify you are running commands in the requested namespace by adding `-n <namespace>` or switching context with `kubectl config set-context --current --namespace=<ns>`.

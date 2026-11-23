# 04 - ServiceAccounts

## 📌 Overview
A **ServiceAccount** provides an identity for processes that run in a Pod. When in-cluster processes authenticate to the API server, they are authenticated as a particular ServiceAccount (e.g., `default`).

---

## 🚀 CKA Fast Imperative Commands

```bash
# Create a ServiceAccount
kubectl create serviceaccount app-sa
kubectl create sa dashboard-sa -n dev

# Generate a time-bound token for a ServiceAccount (K8s 1.24+)
kubectl create token dashboard-sa --duration=2h

# Describe ServiceAccount
kubectl describe sa app-sa
```

---

## 📄 Manifest Examples

### 1. ServiceAccount Manifest
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: dev
```

### 2. Pod Using ServiceAccount
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sa-pod
  namespace: dev
spec:
  serviceAccountName: app-service-account
  # Optional security best practice if pod doesn't need API access:
  # automountServiceAccountToken: false
  containers:
  - name: my-app
    image: nginx
```

---

## 🛠️ Verification
```bash
# Exec into pod to inspect mounted SA token and CA cert
kubectl exec -it sa-pod -n dev -- ls -la /var/run/secrets/kubernetes.io/serviceaccount/
# Files present: ca.crt, namespace, token
```

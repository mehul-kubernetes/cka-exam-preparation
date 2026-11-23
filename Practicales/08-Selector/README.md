# 08 - Selectors (Equality-based & Set-based)

## 📌 Overview
Unlike labels which identify objects, **Selectors** allow users and Kubernetes controllers to filter and select resources based on label values.

Kubernetes supports two types of selectors:
1. **Equality-Based Selectors**: Filters by equality (`=`, `==`, `!=`). Supported by Services, ReplicationControllers, and `kubectl` queries.
2. **Set-Based Selectors**: Filters by membership (`in`, `notin`, `exists`). Supported by Deployments, ReplicaSets, Job, DaemonSet, and `kubectl` queries.

---

## 🚀 CKA CLI Queries with Selectors (`-l`)

```bash
# Equality: get pods where env equals prod
kubectl get pods -l env=prod

# Inequality: get pods where env is not prod
kubectl get pods -l env!=prod

# Multiple conditions (AND logic)
kubectl get pods -l env=prod,tier=frontend

# Set-based: in
kubectl get pods -l 'env in (prod, staging)'

# Set-based: notin
kubectl get pods -l 'tier notin (backend, db)'

# Key existence: has label 'app' regardless of value
kubectl get pods -l app

# Key non-existence: does not have label 'release'
kubectl get pods -l '!release'
```

---

## 📄 Manifest Example: Set-Based Selector

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: selector-demo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
    matchExpressions:
      - { key: tier, operator: In, values: [frontend, api] }
      - { key: env, operator: NotIn, values: [dev] }
  template:
    metadata:
      labels:
        app: webapp
        tier: frontend
        env: prod
    spec:
      containers:
      - name: nginx
        image: nginx
```

---

## 💡 CKA Exam Tips
- When troubleshooting why a Service cannot route traffic to Pods, compare `kubectl get ep <service-name>` with `kubectl get pods -l <selector-keys>`.
- Quote complex expressions in the terminal (`'env in (dev,test)'`) to prevent bash from interpreting parentheses.

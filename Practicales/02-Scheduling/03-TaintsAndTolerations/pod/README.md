# Pods with Tolerations

## 📌 Overview
This directory demonstrates single Pod specifications containing tolerations to overcome node taints.

---

## 📄 Manifest Examples

### Dev Pod (`my-pod-dev.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-dev
  labels:
    env: dev
spec:
  containers:
  - name: nginx
    image: nginx:alpine
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "dev"
    effect: "NoSchedule"
```

### Prod Pod (`my-pod-prod.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-prod
  labels:
    env: prod
spec:
  containers:
  - name: nginx
    image: nginx:alpine
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "prod"
    effect: "NoSchedule"
```

---

## 💡 Important Note on Tolerations
Tolerations **do not** guarantee that a Pod will be scheduled onto the tainted node. They only allow the pod to be accepted if scheduled there. To force a Pod to schedule on a specific node, combine **Tolerations** with **NodeAffinity** or **nodeSelector**.

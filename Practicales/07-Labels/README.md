# 07 - Labels in Kubernetes

## 📌 Overview
**Labels** are key/value pairs that are attached to objects, such as Pods, Services, and Nodes. Labels are intended to specify identifying attributes of objects that are meaningful and relevant to users, without directly implying semantics to the core system.

---

## 🚀 CKA Fast Imperative Commands

```bash
# Create pod with labels
kubectl run nginx --image=nginx -l env=prod,tier=frontend,release=v1.0

# Add a label to an existing pod
kubectl label pod nginx tier=web

# Overwrite / update an existing label (--overwrite flag required)
kubectl label pod nginx env=staging --overwrite

# Remove a label (suffix key with minus '-')
kubectl label pod nginx tier-

# Show all labels when listing pods
kubectl get pods --show-labels

# Label a worker node (vital for NodeSelector and NodeAffinity)
kubectl label node node01 disktype=ssd
kubectl label node node01 color=blue --overwrite

# Remove label from node
kubectl label node node01 disktype-
```

---

## 📄 Manifest Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: labeled-pod
  labels:
    app: payment
    environment: production
    team: backend
spec:
  containers:
  - name: app
    image: nginx
```

---

## 💡 CKA Exam Tips
- Label keys have two parts: an optional prefix and name, separated by a slash (`/`).
- In CKA troubleshooting tasks, mismatched labels between Deployments, ReplicaSets, and Services are among the most common bugs you will need to identify and fix!

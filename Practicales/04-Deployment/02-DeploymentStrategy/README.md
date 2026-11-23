# Deployment Strategies (RollingUpdate vs Recreate)

## 📌 Overview
Kubernetes supports two primary strategies when updating Pods in a Deployment:

1. **RollingUpdate** (Default):
   - Gradually replaces old pods with new ones without downtime.
   - Controlled by `maxUnavailable` and `maxSurge`.
2. **Recreate**:
   - Terminates all running instances simultaneously before creating new ones.
   - Causes downtime, but avoids running two different application versions concurrently (useful when databases cannot handle two versions of schemas).

---

## 📄 Manifest Examples

### 1. RollingUpdate Strategy
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-deploy
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%         # Maximum number of pods created above desired replicas
      maxUnavailable: 25%   # Maximum number of pods that can be unavailable during update
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx:1.24
```

### 2. Recreate Strategy
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: recreate-deploy
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: redis
        image: redis:alpine
```

---

## 🛠️ Verification & Testing
```bash
# Apply rolling update deployment
kubectl apply -f rolling-update.yaml

# Watch pods update sequentially in real time
kubectl set image deployment/rolling-deploy nginx=nginx:1.25
kubectl get pods -l app=web -w
```

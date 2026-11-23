# 02 - Deployment Strategies

## 📌 Overview
When updating Pods, Kubernetes Deployments support two primary strategies configured under `spec.strategy`:

1. **`RollingUpdate`** (Default):
   - Updates pods progressively in batches without service interruption.
   - Configured via `maxSurge` and `maxUnavailable`.
2. **`Recreate`**:
   - Terminates all existing pods at once before creating new ones.
   - Introduces temporary downtime, but guarantees only one version of the application runs at any point in time.

---

## ⚙️ Configuring RollingUpdate Parameters

```yaml
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%         # Can be percentage or absolute number (e.g. 1)
      maxUnavailable: 25%   # Can be percentage or absolute number (e.g. 0)
```

- **`maxSurge`**: Maximum number of pods that can be created above the desired replica count.
- **`maxUnavailable`**: Maximum number of pods that can be unavailable during the update process. Setting `maxUnavailable: 0` ensures 100% capacity is retained during rollouts.

---

## 📄 Manifest Example: Recreate Strategy

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: batch-processor
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: batch
  template:
    metadata:
      labels:
        app: batch
    spec:
      containers:
      - name: processor
        image: alpine
        command: ["sleep", "3600"]
```

---

## 🛠️ Verification
```bash
# Watch pod termination and recreation
kubectl set image deployment/batch-processor processor=alpine:3.19
kubectl get pods -l app=batch -w
# With Recreate, you will observe all 3 pods terminating before new ones start!
```

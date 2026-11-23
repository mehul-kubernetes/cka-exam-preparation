# 02 - ReplicationController (RC)

## 📌 Overview
A **ReplicationController** ensures that a specified number of Pod replicas are running at any given time. If there are too many Pods, it kills the extras; if there are too few, it starts more.

> [!NOTE]
> ReplicationController is the legacy predecessor of **ReplicaSet**. In modern Kubernetes and CKA exams, **Deployments** and **ReplicaSets** are preferred. However, understanding RC is essential for legacy workloads and exam theory.

### Key Difference: ReplicationController vs ReplicaSet
- **ReplicationController**: Supports only **equality-based** selectors (e.g., `environment = production`).
- **ReplicaSet**: Supports **set-based** selectors (e.g., `environment in (production, qa)`), in addition to equality-based selectors.

---

## 📄 Manifest Example: `rc-definition.yaml`

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    app: myapp
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
    spec:
      containers:
      - name: nginx
        image: nginx:1.24
        ports:
        - containerPort: 80
```

---

## 🛠️ Practical Commands & Verification

1. **Create the ReplicationController**:
   ```bash
   kubectl apply -f rc-definition.yaml
   ```

2. **List ReplicationControllers**:
   ```bash
   kubectl get rc
   # Output shows: DESIRED, CURRENT, READY
   ```

3. **Check Managed Pods**:
   ```bash
   kubectl get pods -l app=myapp
   ```

4. **Scale Replicas (Imperatively)**:
   ```bash
   kubectl scale rc myapp-rc --replicas=5
   ```

5. **Test Self-Healing**:
   ```bash
   # Delete one pod and watch RC automatically spin up a replacement
   kubectl delete pod <one-pod-name>
   kubectl get pods -l app=myapp
   ```

6. **Clean up**:
   ```bash
   kubectl delete rc myapp-rc
   ```

---

## 💡 CKA Exam Tips
- The `selector` in RC must match the labels defined under `template.metadata.labels`.
- Today, almost all CKA exam questions expect **Deployments** rather than raw ReplicationControllers.

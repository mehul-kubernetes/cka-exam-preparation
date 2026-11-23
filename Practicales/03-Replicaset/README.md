# 03 - ReplicaSet

## 📌 Overview
A **ReplicaSet**’s purpose is to maintain a stable set of replica Pods running at any given time. It is often used to guarantee the availability of a specified number of identical Pods.

While ReplicaSets can be used independently, in modern Kubernetes they are almost always managed automatically by **Deployments**.

---

## 📄 Manifest Example: `replicaset-definition.yml`

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      name: myapp-pod
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx-container
        image: nginx:alpine
        ports:
        - containerPort: 80
```

### Advanced Selector (`matchExpressions`)
ReplicaSets support set-based filtering:
```yaml
selector:
  matchExpressions:
    - {key: tier, operator: In, values: [frontend, web]}
    - {key: environment, operator: NotIn, values: [dev]}
```

---

## 🛠️ Practical Commands & Verification

1. **Deploy the ReplicaSet**:
   ```bash
   kubectl apply -f replicaset-definition.yml
   ```

2. **Inspect the ReplicaSet**:
   ```bash
   kubectl get rs
   kubectl describe rs myapp-replicaset
   ```

3. **Scale Replicas**:
   ```bash
   # Imperative scaling
   kubectl scale rs myapp-replicaset --replicas=5

   # Declarative scaling (edit manifest and apply or edit in-place)
   kubectl edit rs myapp-replicaset
   ```

4. **Check Pods matching selector**:
   ```bash
   kubectl get pods -l tier=frontend --show-labels
   ```

5. **Clean up**:
   ```bash
   # Delete ReplicaSet and its Pods
   kubectl delete rs myapp-replicaset

   # Delete ReplicaSet WITHOUT deleting its Pods (cascade=orphan)
   kubectl delete rs myapp-replicaset --cascade=orphan
   ```

---

## 💡 CKA Exam Tips
- `apiVersion` for ReplicaSet is `apps/v1`.
- The `spec.selector.matchLabels` **must** match `spec.template.metadata.labels`, or Kubernetes will reject the manifest with a validation error.
- Direct management of ReplicaSets is rarely tested; Deployments are tested far more often because Deployments manage ReplicaSets for seamless rollouts and rollbacks.

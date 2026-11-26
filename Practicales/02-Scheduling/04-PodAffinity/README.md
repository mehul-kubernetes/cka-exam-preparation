# 04 - Inter-Pod Affinity and Anti-Affinity

## 📌 Overview
While **Node Affinity** schedules Pods based on the labels of worker **nodes**, **Inter-Pod Affinity and Anti-Affinity** schedule Pods based on the labels of **other Pods** already running on the nodes.

- **`podAffinity` (Co-location)**: Tells the scheduler to place Pods close to each other (e.g., co-locating a web frontend with a Redis cache on the same node or in the same availability zone to reduce network latency).
- **`podAntiAffinity` (Deduplication / High Availability)**: Tells the scheduler to avoid placing Pods on the same node or zone (e.g., spreading 3 replicas of a critical web app across 3 different physical nodes so no single node failure can cause downtime).

---

## 🔑 Key Concepts

### 1. Hard vs Soft Rules
- **`requiredDuringSchedulingIgnoredDuringExecution`** (Hard Rule): Pod **will not be scheduled** unless the rule is satisfied.
- **`preferredDuringSchedulingIgnoredDuringExecution`** (Soft Rule): Scheduler **attempts** to satisfy the rule, but will place the pod elsewhere if no matching node is available.

### 2. `topologyKey`
The `topologyKey` defines the geographic or logical domain for affinity/anti-affinity:
- `topologyKey: "kubernetes.io/hostname"` $\to$ Schedule on the **same physical Node**.
- `topologyKey: "topology.kubernetes.io/zone"` $\to$ Schedule in the **same Availability Zone**.

---

## 📄 Manifest Examples

### 1. Pod Affinity (Co-locating Web with Cache)
Deploy a Redis cache first:
```bash
kubectl run redis-cache --image=redis -l app=cache
```
Now deploy web application with `podAffinity` to be placed on the **exact same node** as `app=cache`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-frontend
  labels:
    app: web
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - cache
        topologyKey: "kubernetes.io/hostname"
  containers:
  - name: web
    image: nginx
```

---

### 2. Pod Anti-Affinity (Spreading Replicas for High Availability)
Ensures no two replicas of `web-ha` run on the same worker node:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-ha-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-ha
  template:
    metadata:
      labels:
        app: web-ha
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web-ha
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx
        image: nginx:alpine
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy Redis Cache**:
   ```bash
   kubectl run redis-cache --image=redis -l app=cache
   # Check which node redis-cache landed on:
   kubectl get pod redis-cache -o wide
   ```

2. **Deploy Co-located Web Frontend**:
   ```bash
   kubectl apply -f web-affinity.yaml
   # Notice web-frontend is automatically scheduled on the EXACT SAME node as redis-cache!
   kubectl get pod web-frontend -o wide
   ```

3. **Deploy Anti-Affinity Deployment**:
   ```bash
   kubectl apply -f web-anti-affinity.yaml
   kubectl get pods -l app=web-ha -o wide
   # Notice every replica is spread out across different nodes!
   ```

4. **Test Unschedulable Scenario**:
   ```bash
   # If you have 2 worker nodes and scale web-ha to 3 replicas:
   kubectl scale deployment web-ha-deployment --replicas=3
   # The 3rd pod will remain in 'Pending' state because no node satisfies the hard anti-affinity rule!
   kubectl get pods -l app=web-ha
   ```

5. **Clean up**:
   ```bash
   kubectl delete pod redis-cache web-frontend
   kubectl delete deployment web-ha-deployment
   ```

---

## 💡 CKA Exam Tips
- `topologyKey: "kubernetes.io/hostname"` is mandatory for pod affinity/anti-affinity.
- If a question asks to *"prefer"* spreading pods rather than *forcing* it, use `preferredDuringSchedulingIgnoredDuringExecution` with a `weight: 100`.

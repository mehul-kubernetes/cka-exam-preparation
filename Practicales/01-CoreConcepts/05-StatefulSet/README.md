# 05 - StatefulSets in Kubernetes

## 📌 Overview
A **StatefulSet** is the workload API object used to manage stateful applications. Unlike a Deployment where Pods are interchangeable and ephemeral, StatefulSets maintain a **sticky, persistent identity** for each of their Pods.

### Deployment vs StatefulSet

| Feature | Deployment (Stateless) | StatefulSet (Stateful) |
|---|---|---|
| **Pod Identifiers** | Random hashes (`web-7d849b499-x7z8p`) | Predictable ordinals (`web-0`, `web-1`, `web-2`) |
| **Startup / Teardown** | Random / Parallel | Sequential (0 $\to$ 1 $\to$ 2) / Reverse (2 $\to$ 1 $\to$ 0) |
| **Storage Binding** | Shared volume or ephemeral | Dedicated PV per pod via `volumeClaimTemplates` |
| **Storage on Scale Down** | N/A | Storage volume is **retained** (never auto-deleted) |
| **DNS Resolution** | Shared Service IP | Individual DNS per pod (`web-0.nginx.default...`) |
| **Common Workloads** | Web servers, REST APIs, microservices | Databases (MySQL, PostgreSQL, MongoDB, Cassandra), Kafka, ZooKeeper |

---

## 🔑 Crucial StatefulSet Requirements

1. **Headless Service**: A StatefulSet **requires** a Headless Service (`clusterIP: None`) specified in `spec.serviceName` to control the network domain of the Pods.
2. **`volumeClaimTemplates`**: Automatically provisions a dedicated PersistentVolumeClaim (PVC) for each Pod replica.

---

## 📄 Manifest Example: `statefulset-demo.yaml`

```yaml
# 1. Headless Service (clusterIP: None)
apiVersion: v1
kind: Service
metadata:
  name: nginx-headless
  labels:
    app: stateful-web
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: stateful-web
---
# 2. StatefulSet
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx-headless"
  replicas: 3
  selector:
    matchLabels:
      app: stateful-web
  template:
    metadata:
      labels:
        app: stateful-web
    spec:
      containers:
      - name: nginx
        image: nginx:1.24
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy Headless Service and StatefulSet**:
   ```bash
   kubectl apply -f statefulset-demo.yaml
   ```

2. **Observe Sequential Startup**:
   ```bash
   kubectl get pods -w -l app=stateful-web
   ```
   *Notice*:
   - `web-0` starts first and reaches `Running`.
   - Only after `web-0` is ready does `web-1` start.
   - Finally `web-2` starts.

3. **Inspect Generated PersistentVolumeClaims**:
   ```bash
   kubectl get pvc
   # Output:
   # www-web-0   Bound   ...
   # www-web-1   Bound   ...
   # www-web-2   Bound   ...
   ```

4. **Verify Individual Pod DNS Resolution**:
   ```bash
   kubectl run test-dns --image=busybox:1.28 --rm -it -- restart=Never -- nslookup web-0.nginx-headless
   # Returns the direct IP address of pod web-0!
   ```

5. **Scale Up and Down**:
   ```bash
   # Scale up to 5 replicas
   kubectl scale statefulset web --replicas=5
   kubectl get pods -l app=stateful-web

   # Scale down to 2 replicas
   kubectl scale statefulset web --replicas=2
   # Notice: web-4 terminates first, then web-3 (orderly reverse teardown)

   # Notice that PVCs for web-2, web-3, web-4 are RETAINED to prevent data loss:
   kubectl get pvc
   ```

6. **Clean up**:
   ```bash
   kubectl delete statefulset web
   kubectl delete service nginx-headless
   kubectl delete pvc -l app=stateful-web
   ```

---

## 💡 CKA Exam Tips
- The field `spec.serviceName` in the StatefulSet **must** match the `metadata.name` of the Headless Service.
- Deleting a StatefulSet does **not** delete its underlying PVCs or PVs. You must manually clean up PVCs if desired.

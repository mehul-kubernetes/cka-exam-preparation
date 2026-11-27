# VolumeClaimTemplates

## 1. What is volumeClaimTemplates?

`volumeClaimTemplates` is a feature of Kubernetes StatefulSet that automatically creates a separate PersistentVolumeClaim (PVC) for each Pod.

It is mainly used for stateful applications such as:
- MySQL
- PostgreSQL
- MongoDB
- Elasticsearch
- Kafka

## 2. Why do we need volumeClaimTemplates?

Suppose we create a StatefulSet with 3 replicas.

```yaml
replicas: 3
```

Kubernetes automatically creates three Pods, each with its own PVC.

| Pod Name | PVC Name | Storage |
|---|---|---|
| mysql-0 | mysql-storage-mysql-0 | 5Gi |
| mysql-1 | mysql-storage-mysql-1 | 5Gi |
| mysql-2 | mysql-storage-mysql-2 | 5Gi |

Each Pod gets independent persistent storage.

## 3. YAML Configuration

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 3

  selector:
    matchLabels:
      app: mysql

  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8.0
          volumeMounts:
            - name: mysql-storage
              mountPath: /var/lib/mysql

  volumeClaimTemplates:
    - metadata:
        name: mysql-storage
      spec:
        accessModes:
          - ReadWriteOnce
        storageClassName: standard
        resources:
          requests:
            storage: 5Gi
```

## 4. How does it work?

1. Create a StatefulSet with 3 replicas.
2. Kubernetes creates three Pods: mysql-0, mysql-1 and mysql-2.
3. Kubernetes automatically creates three PVCs.
4. Each PVC requests 5Gi of storage.
5. The StorageClass dynamically provisions PVs if configured with a suitable provisioner.
6. Each Pod mounts its own PVC at `/var/lib/mysql`.

## 5. What happens if a Pod is deleted?

Suppose `mysql-1` gets deleted.

- Kubernetes recreates the Pod with the same name, `mysql-1`.
- The existing PVC is reused.
- Previously stored data remains available, provided the underlying storage is retained and accessible.

**Important:** StatefulSet Pods have stable identities and their PVCs can persist across Pod recreation.

## 6. Difference between volumes, volumeMounts and volumeClaimTemplates

| Feature | Description |
|---|---|
| volumes | Defines storage available to a Pod |
| volumeMounts | Mounts storage inside a container |
| volumeClaimTemplates | Automatically creates individual PVCs for StatefulSet Pods |

## 7. Important Commands

Check StatefulSets:

```bash
kubectl get statefulsets
```

Check Pods:

```bash
kubectl get pods
```

Check PVCs:

```bash
kubectl get pvc
```

Check PVs:

```bash
kubectl get pv
```

Describe StatefulSet:

```bash
kubectl describe statefulset mysql
```

## 8. Interview Questions

**Q1. What is volumeClaimTemplates in Kubernetes?**

It automatically creates a separate PVC for each Pod in a StatefulSet.

**Q2. Can we use volumeClaimTemplates in Deployment?**

No. `volumeClaimTemplates` is a StatefulSet specification feature.

**Q3. What happens to PVCs when a StatefulSet Pod is deleted?**

The PVC normally remains, and the recreated Pod reuses it.

**Q4. Does each StatefulSet Pod get its own PVC?**

Yes, each Pod ordinal gets its own PVC from the volumeClaimTemplates.

**Q5. What is the main advantage of volumeClaimTemplates?**

It provides independent persistent storage for each StatefulSet Pod while maintaining stable storage identity.
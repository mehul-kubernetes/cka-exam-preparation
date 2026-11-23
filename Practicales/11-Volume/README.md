# 11 - Kubernetes Volumes

## 📌 Overview
On-disk files in a container are ephemeral, which presents issues for non-trivial applications when running in containers:
1. When a container crashes, kubelet restarts it with a clean slate.
2. Sharing files between containers running simultaneously in a Pod.

Kubernetes **Volumes** solve both problems by abstracting storage lifecycles.

---

## 🔑 Common Volume Types in CKA

| Volume Type | Description | Persistence |
|---|---|---|
| `emptyDir` | Initialized empty when Pod is assigned to a Node. Shared across containers in the same Pod. | Deleted when Pod is removed from node. |
| `hostPath` | Mounts a file or directory from the host worker node's filesystem into the Pod. | Remains on node filesystem after pod termination. |
| `configMap` / `secret` | Injects configuration data or sensitive keys as files into container directories. | Managed by Kubernetes API. |
| `persistentVolumeClaim` | Binds a PersistentVolume to the Pod decoupled from node or pod lifecycles. | Independent lifecycle. |

---

## 📄 Manifest Examples

### 1. `emptyDir` Volume (Shared between containers)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-pod
spec:
  containers:
  - name: producer
    image: alpine
    command: ["/bin/sh", "-c", "while true; do date >> /cache/data.txt; sleep 5; done"]
    volumeMounts:
    - name: shared-cache
      mountPath: /cache
  - name: consumer
    image: busybox
    command: ["/bin/sh", "-c", "tail -f /cache/data.txt"]
    volumeMounts:
    - name: shared-cache
      mountPath: /cache
  volumes:
  - name: shared-cache
    emptyDir: {}
```

### 2. `hostPath` Volume (`myvol1.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-hostpath
spec:
  containers:
  - name: redis
    image: redis
    volumeMounts:
    - name: redis-storage
      mountPath: /data/redis
  volumes:
  - name: redis-storage
    hostPath:
      path: /var/log/nginx
      type: DirectoryOrCreate
```

---

## 🛠️ Verification
```bash
# Deploy pod with volume
kubectl apply -f myvol1.yaml

# Exec into pod to inspect mounted volume
kubectl exec -it redis-hostpath -- df -h /data/redis
kubectl exec -it redis-hostpath -- touch /data/redis/test.txt
```

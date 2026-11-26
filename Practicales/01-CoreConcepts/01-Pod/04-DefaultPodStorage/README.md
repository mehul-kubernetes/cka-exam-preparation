# 04 - Default Pod Storage & Ephemeral Writable Layer

## 📌 Overview
By default, every container running inside a Kubernetes Pod has an **ephemeral storage layer** (the container's root filesystem writable layer provided by the container runtime via OverlayFS).

Any files written to directories inside the container that are **not mounted to a Kubernetes Volume** are written directly to this ephemeral storage layer.

---

## ⚠️ The Problem with Default Container Storage

1. **Non-persistent across restarts**: If the container process crashes or restarts (e.g. `CrashLoopBackOff` or `OOMKilled`), the container's writable layer is deleted and recreated with a fresh state. **All data written to it is permanently lost.**
2. **Non-sharable**: Other containers in the same Pod cannot access files written to another container's root filesystem.
3. **Local node disk consumption**: Default storage writes consume the worker node's root filesystem (`/var/lib/containerd` or `/var/lib/docker`), which can cause disk pressure and node eviction (`DiskPressure` condition).

---

## 🛠️ Step-by-Step Hands-On Exercise

### 1. Launch Pod with Default Storage
```bash
kubectl run pod-default-storage --image=busybox --restart=Always --command -- sleep 3600
```

### 2. Write Data to the Container Writable Layer
```bash
# Write data inside /tmp
kubectl exec pod-default-storage -- sh -c 'echo "hello-from-container" > /tmp/message.txt'

# Verify file exists
kubectl exec pod-default-storage -- cat /tmp/message.txt
# Output: hello-from-container
```

### 3. Simulate Container Process Crash & Restart
```bash
# Kill the sleep process inside container (PID 1)
kubectl exec pod-default-storage -- kill 1

# Check pod restarts incremented:
kubectl get pod pod-default-storage -w
# RESTARTS will change from 0 to 1
```

### 4. Verify Data Loss
```bash
# Attempt to read the file again after restart
kubectl exec pod-default-storage -- cat /tmp/message.txt
# Output: cat: can't open '/tmp/message.txt': No such file or directory
```
*Result*: The data was wiped out because it was stored on the ephemeral container root layer!

---

## 💡 How to Solve This: Kubernetes Volumes

To preserve data across container restarts:
1. **`emptyDir`**: Preserves data across container restarts within the Pod (only deleted when the entire Pod is deleted).
2. **`PersistentVolumeClaim` (PVC)**: Preserves data permanently across Pod deletions, restarts, and node rescheduling.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-emptydir
spec:
  containers:
  - name: app
    image: busybox
    command: ["sleep", "3600"]
    volumeMounts:
    - name: safe-storage
      mountPath: /tmp
  volumes:
  - name: safe-storage
    emptyDir: {}
```

---

## 🧹 Cleanup
```bash
kubectl delete pod pod-default-storage
```

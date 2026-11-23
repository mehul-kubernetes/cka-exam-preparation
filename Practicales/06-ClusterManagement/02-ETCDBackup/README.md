# 02 - ETCD Backup and Restore

## 📌 Overview
**ETCD** is Kubernetes' distributed key-value store containing the entire state and configuration of the cluster. Backing up and restoring ETCD is one of the most heavily tested domains in the **CKA Exam**.

---

## 🔑 Crucial ETCD Files & Paths
Check `/etc/kubernetes/manifests/etcd.yaml` to identify the required TLS certificates:
- **`--endpoints`**: `https://127.0.0.1:2379`
- **`--cacert`**: `/etc/kubernetes/pki/etcd/ca.crt`
- **`--cert`**: `/etc/kubernetes/pki/etcd/server.crt`
- **`--key`**: `/etc/kubernetes/pki/etcd/server.key`

---

## 🛠️ Task 1: Taking an ETCD Snapshot

```bash
# Set ETCD API version to 3
export ETCDCTL_API=3

# Run snapshot save
etcdctl snapshot save /opt/etcd-backup.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# Verify snapshot integrity
etcdctl snapshot status /opt/etcd-backup.db --write-out=table
```

---

## 🛠️ Task 2: Restoring ETCD from Snapshot

1. **Restore snapshot to a new data directory**:
   ```bash
   export ETCDCTL_API=3

   etcdctl snapshot restore /opt/etcd-backup.db \
     --data-dir=/var/lib/etcd-restore
   ```

2. **Update ETCD Static Pod Manifest**:
   Edit `/etc/kubernetes/manifests/etcd.yaml`:
   ```yaml
   spec:
     containers:
     - command:
       - etcd
       # Update this line:
       - --data-dir=/var/lib/etcd-restore
       ...
       volumeMounts:
       - mountPath: /var/lib/etcd-restore
         name: etcd-data
     volumes:
     - hostPath:
         # Update this hostPath:
         path: /var/lib/etcd-restore
         type: DirectoryOrCreate
       name: etcd-data
   ```

3. **Wait for Kubelet to Reload the Static Pod**:
   Kubelet automatically detects changes in `/etc/kubernetes/manifests/` and restarts the ETCD container.
   ```bash
   kubectl get pods -n kube-system
   ```

---

## 💡 CKA Exam Tips
- Always test with `etcdctl snapshot status <filename>` before moving on.
- Never overwrite the original `/var/lib/etcd` directly while the pod is running; restore to a new directory and update `/etc/kubernetes/manifests/etcd.yaml`.

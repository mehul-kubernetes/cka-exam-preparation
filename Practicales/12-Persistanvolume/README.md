# 12 - PersistentVolumes (PV)

## 📌 Overview
A **PersistentVolume (PV)** is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.
PVs are **cluster-scoped** resources (independent of any individual Pod and not tied to any Namespace).

---

## 🔑 Key Concepts & Lifecycle

### 1. Access Modes
- **`ReadWriteOnce` (RWO)**: Mounted as read-write by a single Node.
- **`ReadOnlyMany` (ROX)**: Mounted read-only by many Nodes.
- **`ReadWriteMany` (RWX)**: Mounted as read-write by many Nodes.
- **`ReadWriteOncePod` (RWOP)**: Mounted as read-write by a single Pod (introduced in K8s 1.22+).

### 2. Reclaim Policies
- **`Retain`**: Manual reclamation. When PVC is deleted, the PV still exists and data remains intact, but PV status becomes `Released`.
- **`Delete`**: Associated storage asset (e.g., AWS EBS, GCE PD) is automatically deleted.
- **`Recycle`**: Basic scrub (`rm -rf /thevolume/*`) to make it available for another claim (deprecated in favor of dynamic provisioning).

---

## 📄 Manifest Example: `pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mypv
  labels:
    type: local
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: ""          # Empty string explicitly specifies no storage class (static binding)
  hostPath:
    path: /data
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Create the PersistentVolume**:
   ```bash
   kubectl apply -f pv.yaml
   ```

2. **Inspect the PersistentVolume**:
   ```bash
   kubectl get pv
   # Expected status: Available
   kubectl describe pv mypv
   ```

3. **Verify Cluster Scope**:
   ```bash
   # Note: PV is NOT namespaced. Specifying -n dev returns the exact same cluster PV.
   kubectl get pv -n default
   kubectl get pv -n kube-system
   ```

4. **Clean up**:
   ```bash
   kubectl delete pv mypv
   ```

---

## 💡 CKA Exam Tips
- **PV is cluster-wide**: PVC is namespaced.
- If a PVC is stuck in `Pending`, verify if the PV's capacity is $\ge$ requested storage, that accessModes match, and that `storageClassName` values match identically (or both are empty `""`).

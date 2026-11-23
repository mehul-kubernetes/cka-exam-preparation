# 04 - Kubernetes Storage

## 📌 Overview
The **Storage** domain accounts for **10%** of the CKA exam. You must understand how Kubernetes handles ephemeral vs persistent storage, how to define cluster-wide storage pools, and how to dynamically provision storage on demand.

---

## 📂 Subtopics in this Module

| Subdirectory | Resource | Scope | Key Focus |
|---|---|---|---|
| [`01-Volume`](./01-Volume/README.md) | **Volumes** | Pod | `emptyDir` (shared temporary), `hostPath` (node filesystem). |
| [`02-Persistanvolume`](./02-Persistanvolume/README.md) | **PersistentVolume (PV)** | Cluster | Storage capacity, access modes (`RWO`, `ROX`, `RWX`), reclaim policies (`Retain`, `Delete`). |
| [`04-PersistantVolumeClaim`](./04-PersistantVolumeClaim/README.md) | **PVC** | Namespace | Requesting storage, matching capacity/accessMode, binding lifecycle. |
| [`05-StorageClass`](./05-StorageClass/ReadMe.md) | **StorageClass** | Cluster | Dynamic provisioning, provisioners, `WaitForFirstConsumer` vs `Immediate`. |

---

## 🔄 The Storage Lifecycle
```
+-------------------------------------------------------------+
|                     StorageClass                            |
|             (Defines dynamic provisioner)                   |
+------------------------------+------------------------------+
                               |
                               v (auto-creates PV)
+------------------------+             +------------------------+
|    PersistentVolume    | <=========> | PersistentVolumeClaim  |
|    (Cluster Storage)   |   (Bound)   |    (Namespace Claim)   |
+------------------------+             +-----------+------------+
                                                   |
                                                   v (mounted via volume)
                                        +---------------------+
                                        |         Pod         |
                                        +---------------------+
```

---

## 💡 CKA Exam Tips
- Remember: **PV is cluster-scoped**; **PVC is namespaced**.
- When a PVC is deleted, the PV’s fate depends on `persistentVolumeReclaimPolicy`:
  - `Retain`: PV becomes `Released` and data is preserved (cannot be bound to a new PVC until manually recycled).
  - `Delete`: Storage asset is deleted immediately.

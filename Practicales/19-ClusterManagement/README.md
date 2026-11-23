# 19 - Cluster Management & Maintenance

## 📌 Overview
Cluster management accounts for **25% (Cluster Architecture, Installation & Configuration)** of the CKA exam score. You are guaranteed to encounter questions on:
1. **Cluster Installation**: Setting up a multi-node Kubernetes cluster using `kubeadm`.
2. **ETCD Backup & Restore**: Taking a snapshot of ETCD and restoring it after failure.
3. **Cluster Version Upgrades**: Upgrading control-plane and worker nodes safely with zero downtime.

---

## 📁 Subtopics in this Directory

| Subdirectory | Topic | Key Focus |
|---|---|---|
| [`01-Installation`](./01-Installation/README.md) | Kubeadm Cluster Setup | Container runtime (`containerd`), `kubeadm init`, CNI plugin, `kubeadm join`. |
| [`02-ETCDBackup`](./02-ETCDBackup/README.md) | ETCD Snapshot & Restore | `etcdctl snapshot save`, TLS certs, static pod manifests update. |
| [`03-VersionUpgrade`](./03-VersionUpgrade/README.md) | Kubeadm Upgrade Lifecycle | Draining nodes, updating `kubeadm`/`kubelet`/`kubectl`, `uncordon`. |

---

## 🚀 Quick Reference Commands
```bash
# Node draining & cordon
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
kubectl cordon <node-name>
kubectl uncordon <node-name>

# Check node & component status
kubectl get nodes -o wide
kubectl get componentstatuses # (legacy)
kubectl get pods -n kube-system
```

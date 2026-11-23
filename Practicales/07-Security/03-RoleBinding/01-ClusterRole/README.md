# ClusterRole

## 📌 Overview
A **ClusterRole** is a non-namespaced resource used to define permissions across the entire cluster, or for non-namespaced resources (like Nodes, Namespaces, StorageClasses, and PersistentVolumes).

---

## 🚀 CKA Fast Imperative Commands

```bash
# Create ClusterRole for node monitoring
kubectl create clusterrole node-reader \
  --verb=get,list,watch \
  --resource=nodes

# Create ClusterRole for PV management
kubectl create clusterrole pv-admin \
  --verb=* \
  --resource=persistentvolumes,persistentvolumeclaims
```

---

## 📄 Manifest Example

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```

---

## 💡 Dual Use of ClusterRole
A `ClusterRole` can be bound using:
1. **`ClusterRoleBinding`**: Grants permissions cluster-wide across all namespaces.
2. **`RoleBinding`**: Grants permissions **only within the namespace** of the `RoleBinding`. This is a very common CKA pattern to reuse common roles across multiple namespaces!

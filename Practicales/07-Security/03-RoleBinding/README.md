# 03 - RBAC Roles & RoleBindings

## 📌 Overview
Role-Based Access Control (**RBAC**) uses the `rbac.authorization.k8s.io` API group to drive authorization decisions.

### Namespaced vs Cluster-Scoped:
- **`Role`**: Grants permissions within a specific namespace.
- **`RoleBinding`**: Binds a `Role` (or `ClusterRole`) to a user, group, or service account **within a specific namespace**.
- **`ClusterRole`**: Grants permissions across all namespaces or cluster-scoped resources (e.g., Nodes, PVs, namespaces).
- **`ClusterRoleBinding`**: Binds a `ClusterRole` cluster-wide.

---

## 🚀 CKA Fast Imperative Commands

```bash
# 1. Create a Role in namespace 'dev'
kubectl create role pod-reader \
  --verb=get,list,watch \
  --resource=pods \
  --namespace=dev

# 2. Create a Role with multiple resources and verbs
kubectl create role app-manager \
  --verb=create,get,update,delete \
  --resource=deployments,services \
  --namespace=dev

# 3. Create a RoleBinding
kubectl create rolebinding read-pods-binding \
  --role=pod-reader \
  --user=jane \
  --namespace=dev

# 4. Bind to a ServiceAccount
kubectl create rolebinding sa-pod-binding \
  --role=pod-reader \
  --serviceaccount=dev:my-sa \
  --namespace=dev
```

---

## 📄 Manifest Example: `Role` & `RoleBinding`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

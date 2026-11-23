# ClusterRoleBinding

## 📌 Overview
A **ClusterRoleBinding** grants permissions defined in a `ClusterRole` to users, groups, or service accounts across the entire cluster.

---

## 🚀 CKA Fast Imperative Commands

```bash
# Bind ClusterRole to a User across the cluster
kubectl create clusterrolebinding read-nodes-binding \
  --clusterrole=node-reader \
  --user=john

# Bind ClusterRole to a Group
kubectl create clusterrolebinding dev-cluster-binding \
  --clusterrole=view \
  --group=developers

# Bind cluster-admin role to a ServiceAccount
kubectl create clusterrolebinding cicd-admin-binding \
  --clusterrole=cluster-admin \
  --serviceaccount=kube-system:cicd-sa
```

---

## 📄 Manifest Example

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: global-node-reader
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: node-reader
  apiGroup: rbac.authorization.k8s.io
```

---

## 🔍 Verification
```bash
# Verify permissions using 'kubectl auth can-i'
kubectl auth can-i get nodes --as=john
# Output: yes

kubectl auth can-i delete pods --as=john
# Output: no
```

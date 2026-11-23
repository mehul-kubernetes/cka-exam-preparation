# 10 - Node Affinity & nodeSelector

## 📌 Overview
**Node Affinity** is a set of rules used by the scheduler to determine where a Pod can be placed. It is a major enhancement over the basic `nodeSelector` mechanism.

### Affinity Types:
1. **`requiredDuringSchedulingIgnoredDuringExecution`** (Hard affinity):
   - The scheduler **must** satisfy the rule to place the pod on the node.
   - If no matching node exists, the pod remains in `Pending` state.
2. **`preferredDuringSchedulingIgnoredDuringExecution`** (Soft affinity):
   - The scheduler will **try** to find a matching node, but will schedule the pod elsewhere if no match is found.
3. *Note*: `IgnoredDuringExecution` means if labels on the node change later while the pod is running, the pod will not be evicted.

---

## 🚀 CKA CLI Commands

```bash
# 1. Label nodes
kubectl label node node01 color=blue
kubectl label node node02 size=large

# 2. View node labels
kubectl get nodes --show-labels
kubectl get nodes -L color,size
```

---

## 📄 Manifest Examples

### 1. Simple `nodeSelector`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nodeselector-pod
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    color: blue
```

### 2. Node Affinity (Hard Rule)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hard-affinity-pod
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: color
            operator: In
            values:
            - blue
            - green
```

### 3. Node Affinity (Soft Rule with Weights)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: soft-affinity-pod
spec:
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 80
        preference:
          matchExpressions:
          - key: color
            operator: In
            values:
            - blue
```

---

## 💡 Operators Supported
- `In`: Label value is in the specified list.
- `NotIn`: Label value is not in the list.
- `Exists`: Node has label with the specified key.
- `DoesNotExist`: Node does not have label with specified key.
- `Gt` / `Lt`: Greater than / less than integer comparison.

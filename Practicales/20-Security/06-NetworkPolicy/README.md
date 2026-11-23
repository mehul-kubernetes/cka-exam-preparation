# 06 - Network Policies

## 📌 Overview
By default, Pods in Kubernetes are **non-isolated**: they accept traffic from any source.
A **NetworkPolicy** is a specification of how groups of Pods are allowed to communicate with each other and other network endpoints.

> [!IMPORTANT]
> NetworkPolicies require a CNI plugin that supports policy enforcement (such as **Calico**, **Cilium**, or **Weave Net**). Default Flannel does not support NetworkPolicies.

---

## 🔑 Key Concepts
- **`podSelector`**: Selects the Pods to which the policy applies.
- **`policyTypes`**: Specifies whether the policy applies to `Ingress` (incoming traffic), `Egress` (outgoing traffic), or both.
- **`ingress.from` / `egress.to` Rules**:
  - `podSelector`: Only pods matching specific labels.
  - `namespaceSelector`: Only pods in namespaces matching specific labels.
  - `ipBlock`: Specific CIDR ranges (e.g. `10.0.0.0/24`).
- **`ports`**: Specific destination ports and protocols.

---

## 📄 Manifest Examples

### 1. Default Deny All Ingress Traffic
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {} # Applies to ALL pods in namespace
  policyTypes:
  - Ingress
```

### 2. Allow Ingress Only from Frontend Pods on Port 3306 (Database Policy)
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-netpol
  namespace: prod
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 3306
```

### 3. Allow Ingress from a Specific Namespace
```yaml
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        project: marketing
```

---

## 🛠️ Verification
```bash
# Test connection from unauthorized pod
kubectl run test-unauthorized --image=busybox --rm -it -- restart=Never -- nc -zv db-service 3306
# Expected: Connection timed out / refused

# Test connection from authorized pod
kubectl run test-authorized --image=busybox -l role=frontend --rm -it -- restart=Never -- nc -zv db-service 3306
# Expected: open
```

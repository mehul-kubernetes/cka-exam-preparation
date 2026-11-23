# 09 - Taints and Tolerations

## 📌 Overview
**Taints** allow a Node to repel a set of Pods.
**Tolerations** are applied to Pods and allow (but do not require) the Pods to schedule onto Nodes with matching taints.

Taints and tolerations work together to ensure that Pods are not scheduled onto inappropriate nodes. A common use case is reserving control-plane nodes or dedicated GPU/production nodes.

---

## 🔑 Taint Effects

| Effect | Behavior |
|---|---|
| `NoSchedule` | Pod will **not** be scheduled onto the node unless it has a matching toleration. Existing pods on the node are unaffected. |
| `PreferNoSchedule` | The scheduler will **try** to avoid scheduling the pod onto the node, but it is not guaranteed. |
| `NoExecute` | New pods will not be scheduled, and existing pods on the node that do not tolerate the taint are immediately evicted. |

---

## 🚀 CKA Fast Imperative Commands

```bash
# Add a taint to a worker node
kubectl taint nodes node01 app=prod:NoSchedule
kubectl taint nodes node01 tier=frontend:NoExecute

# Inspect taints on a node
kubectl describe node node01 | grep -i taints

# Remove a taint from a node (append '-' at the end)
kubectl taint nodes node01 app=prod:NoSchedule-
kubectl taint nodes node01 tier=frontend:NoExecute-

# Remove all taints matching key
kubectl taint nodes node01 app-
```

---

## 📄 Manifest Example: Toleration in Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-prod
  labels:
    env: prod
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "prod"
    effect: "NoSchedule"
```

### Toleration with `operator: "Exists"`
Tolerates any value for key `dedicated`:
```yaml
tolerations:
- key: "dedicated"
  operator: "Exists"
  effect: "NoSchedule"
```

---

## 📁 Subdirectories
- `pod/`: Sample Pod manifests with tolerations (`my-pod-dev.yaml`, `my-pod-prod.yaml`).
- `deployment/`: Sample Deployment manifests with tolerations (`my-deploy-dev.yaml`, `my-deploy-prod.yaml`).

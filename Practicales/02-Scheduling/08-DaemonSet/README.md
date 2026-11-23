# 08 - DaemonSets

## 📌 Overview
A **DaemonSet** ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected.

### Typical DaemonSet Use Cases:
- **Cluster Storage Daemons**: `ceph`, `glusterd`.
- **Node Log Collection Daemons**: `fluentd`, `logstash`, `promtail`.
- **Node Monitoring Daemons**: `node-exporter`, `datadog-agent`.
- **CNI Networking Daemons**: `kube-flannel-ds`, `calico-node`, `weave-net`.

---

## 🚀 CKA Fast DaemonSet Trick

`kubectl` does not have an imperative `kubectl create daemonset` command.
Use this time-saving exam shortcut:

```bash
# 1. Create a deployment manifest via dry-run
kubectl create deployment my-daemon --image=fluentd --dry-run=client -o yaml > daemonset.yaml

# 2. Edit daemonset.yaml:
#    - Change 'kind: Deployment' -> 'kind: DaemonSet'
#    - Remove 'replicas: 1'
#    - Remove 'strategy: ...'

# 3. Apply the DaemonSet
kubectl apply -f daemonset.yaml
```

---

## 📄 Manifest Example: `daemonset.yaml`

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-monitor
  labels:
    tier: monitoring
spec:
  selector:
    matchLabels:
      name: node-monitor
  template:
    metadata:
      labels:
        name: node-monitor
    spec:
      containers:
      - name: node-exporter
        image: prom/node-exporter:v1.7.0
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 100Mi
```

---

## 🛠️ Verification & Node Selectors
```bash
# List all DaemonSets and desired/ready replica count (matches node count)
kubectl get ds -o wide

# Running DaemonSets only on specific worker nodes:
# Add 'nodeSelector' or 'nodeAffinity' to template spec
spec:
  template:
    spec:
      nodeSelector:
        disk: ssd
```

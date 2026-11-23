# Weave Net CNI Installation & Verification

## 📌 Overview
This directory contains the manifest `weave-daemonset-k8s.yaml` used to deploy **Weave Net** as a DaemonSet across all nodes in a Kubernetes cluster.

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy Weave Net**:
   ```bash
   kubectl apply -f weave-daemonset-k8s.yaml
   ```

2. **Verify DaemonSet and Pods in `kube-system`**:
   ```bash
   kubectl get ds -n kube-system weave-net
   kubectl get pods -n kube-system -l name=weave-net -o wide
   # Each node will run one weave-net pod
   ```

3. **Verify Node Readiness**:
   ```bash
   kubectl get nodes
   # Nodes should transition to Ready once the CNI is healthy
   ```

4. **CNI Configuration Directory on Nodes**:
   Inspect the active CNI configuration files on worker nodes:
   ```bash
   ls -la /etc/cni/net.d/
   ls -la /opt/cni/bin/
   ```

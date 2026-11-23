# 03 - Cluster Version Upgrade using Kubeadm

## 📌 Overview
Kubernetes clusters must be upgraded sequentially one minor version at a time (e.g., `1.28.x` $\to$ `1.29.x` $\to$ `1.30.x`). Never skip minor versions.

---

## 🛠️ Step-by-Step Upgrade Procedure

### Phase 1: Upgrade the First Control-Plane Node

1. **Find available versions**:
   ```bash
   sudo apt-get update
   sudo apt-cache madison kubeadm
   ```

2. **Upgrade `kubeadm`**:
   ```bash
   sudo apt-mark unhold kubeadm
   sudo apt-get update && sudo apt-get install -y kubeadm=1.30.0-00
   sudo apt-mark hold kubeadm
   ```

3. **Verify and apply upgrade plan**:
   ```bash
   sudo kubeadm upgrade plan
   sudo kubeadm upgrade apply v1.30.0 -y
   ```

4. **Drain the Control Plane node**:
   ```bash
   kubectl drain controlplane --ignore-daemonsets --delete-emptydir-data
   ```

5. **Upgrade `kubelet` and `kubectl`**:
   ```bash
   sudo apt-mark unhold kubelet kubectl
   sudo apt-get install -y kubelet=1.30.0-00 kubectl=1.30.0-00
   sudo apt-mark hold kubelet kubectl

   sudo systemctl daemon-reload
   sudo systemctl restart kubelet
   ```

6. **Uncordon the Control Plane node**:
   ```bash
   kubectl uncordon controlplane
   ```

---

### Phase 2: Upgrade Worker Nodes (e.g., `node01`)

1. **Drain the worker node from the control plane**:
   ```bash
   kubectl drain node01 --ignore-daemonsets --delete-emptydir-data
   ```

2. **SSH into the worker node (`node01`)**:
   ```bash
   ssh node01
   ```

3. **Upgrade `kubeadm` on the worker**:
   ```bash
   sudo apt-mark unhold kubeadm
   sudo apt-get update && sudo apt-get install -y kubeadm=1.30.0-00
   sudo apt-mark hold kubeadm
   ```

4. **Upgrade node configuration**:
   ```bash
   sudo kubeadm upgrade node
   ```

5. **Upgrade `kubelet` and `kubectl` on the worker**:
   ```bash
   sudo apt-mark unhold kubelet kubectl
   sudo apt-get install -y kubelet=1.30.0-00 kubectl=1.30.0-00
   sudo apt-mark hold kubelet kubectl

   sudo systemctl daemon-reload
   sudo systemctl restart kubelet
   exit
   ```

6. **Uncordon the worker node from the control plane**:
   ```bash
   kubectl uncordon node01
   ```

---

## 🔍 Verification
```bash
kubectl get nodes
# All nodes should show v1.30.0 and Ready!
```

# 07 - Static Pods

## 📌 Overview
**Static Pods** are managed directly by the **`kubelet`** daemon on a specific worker or master node, without the API server or control plane managing them.

Static Pods are primarily used to run the core Kubernetes control-plane components (such as `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, and `etcd`) as containers before the Kubernetes API server itself is running.

---

## 🔑 How Static Pods Work
1. The kubelet periodically scans a designated directory for manifest files.
2. Default directory: **`/etc/kubernetes/manifests/`**.
3. Any valid Pod manifest placed in this directory is automatically created by the kubelet.
4. Any manifest deleted from this directory causes the kubelet to automatically delete the Pod.
5. The kubelet creates a **Mirror Pod** in the Kubernetes API server so that `kubectl get pods -n kube-system` displays the pod with the node name appended (e.g., `etcd-controlplane`).

---

## 🔍 How to Identify Static Pod Manifest Directory

To find the configured static pod directory on any node:
```bash
# 1. Inspect kubelet configuration file
grep -i staticPodPath /var/lib/kubelet/config.yaml
# Typical output: staticPodPath: /etc/kubernetes/manifests

# 2. Or check the kubelet systemd service
ps aux | grep kubelet | grep -i "\-\-pod-manifest-path"
```

---

## 🛠️ Step-by-Step Hands-On Exercise

### Task: Create a Static Pod on a Worker Node (`node01`)

1. **SSH to the target node**:
   ```bash
   ssh node01
   ```

2. **Verify static pod directory**:
   ```bash
   grep -i staticPodPath /var/lib/kubelet/config.yaml
   # Output: /etc/kubernetes/manifests
   ```

3. **Generate a Pod manifest in the static pod directory**:
   ```bash
   sudo kubectl run my-static-nginx --image=nginx --dry-run=client -o yaml | sudo tee /etc/kubernetes/manifests/my-static-nginx.yaml
   ```

4. **Return to Control Plane and Verify**:
   ```bash
   exit
   kubectl get pods -o wide
   # You will see: my-static-nginx-node01 running on node01!
   ```

5. **Deleting a Static Pod**:
   Static Pods **cannot** be deleted via `kubectl delete pod` (the kubelet will immediately recreate it). You must delete the file on the node:
   ```bash
   ssh node01
   sudo rm /etc/kubernetes/manifests/my-static-nginx.yaml
   ```

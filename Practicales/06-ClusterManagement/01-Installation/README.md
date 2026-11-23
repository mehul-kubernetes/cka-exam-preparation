# 01 - Cluster Installation & Local Environments

## 📌 Overview
This directory covers both **local cluster tools** for fast developer experimentation and **production multi-node cluster bootstrapping** using `kubeadm` (tested directly in the CKA exam).

---

## 📂 Installation Guides & Environments

| Module | Type | Best For | Link |
|---|:---:|---|---|
| **01 - Minikube** | Local / Single or Multi-Node | Fast learning, addon testing (ingress, metrics-server, dashboard) | [Read Guide](./01-Minikube/README.md) |
| **02 - KinD Cluster** | Local / Docker-in-Docker | Multi-node topology testing, pure `containerd` matching CKA, CI/CD | [Read Guide](./02-KindCluster/README.md) |
| **03 - K3d Cluster** | Local / k3s in Docker | Ultra-low RAM footprint (~512MB), blazing fast startup | [Read Guide](./03-K3dCluster/README.md) |
| **Kubeadm (Production)** | Multi-Node Baremetal / VM | Official CKA exam installation syllabus (Ubuntu/Debian) | *See Full Guide Below* |

---

## 🛠️ Production Kubeadm Cluster Setup (CKA Exam Curriculum)

### Step 1: Prerequisites on ALL Nodes (Control Plane & Workers)

1. **Disable Swap**:
   ```bash
   sudo swapoff -a
   sudo sed -i '/swap/d' /etc/fstab
   ```

2. **Load Kernel Modules & Configure Sysctl**:
   ```bash
   cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
   overlay
   br_netfilter
   EOF

   sudo modprobe overlay
   sudo modprobe br_netfilter

   cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.bridge.bridge-nf-call-iptables  = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   net.ipv4.ip_forward                 = 1
   EOF

   sudo sysctl --system
   ```

3. **Install Container Runtime (`containerd`)**:
   ```bash
   sudo apt-get update && sudo apt-get install -y containerd
   sudo mkdir -p /etc/containerd
   containerd config default | sudo tee /etc/containerd/config.toml
   # Set SystemdCgroup = true
   sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
   sudo systemctl restart containerd
   sudo systemctl enable containerd
   ```

4. **Install `kubeadm`, `kubelet`, and `kubectl`**:
   ```bash
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl gpg

   # Add Kubernetes package repository
   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
   echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

   sudo apt-get update
   sudo apt-get install -y kubelet kubeadm kubectl
   sudo apt-mark hold kubelet kubeadm kubectl
   ```

---

## 🛠️ Step 2: Initialize Control Plane (Master Node Only)

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=<MASTER-IP>

# Set up local kubeconfig
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

## 🛠️ Step 3: Install CNI Network Plugin (e.g. Flannel or Calico)

```bash
# Example: Install Flannel
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

---

## 🛠️ Step 4: Join Worker Nodes

On worker nodes, execute the join command printed during `kubeadm init`:
```bash
sudo kubeadm join <MASTER-IP>:6443 --token <token> \
    --discovery-token-ca-cert-hash sha256:<hash>
```

To regenerate token if expired:
```bash
kubeadm token create --print-join-command
```

---

## 🔍 Verification
```bash
kubectl get nodes
# All nodes should transition from NotReady to Ready!
```

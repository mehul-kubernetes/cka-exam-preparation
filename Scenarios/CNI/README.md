# Container Network Interface (CNI)

## 📌 Overview
Kubernetes imposes fundamental networking requirements:
1. All pods can communicate with all other pods on any node without NAT.
2. All agents on a node (e.g. `kubelet`) can communicate with all pods on that node.
3. Pods see their own IP as the same IP that other pods see.

These requirements are implemented by **CNI (Container Network Interface)** plugins.

---

## 🔑 Common CNI Plugins
- **Weave Net**: Resilient, mesh-based overlay network.
- **Calico**: Highly performant, supports BGP and advanced NetworkPolicy enforcement.
- **Flannel**: Simple, lightweight overlay network using VXLAN.

---

## 📂 Subdirectories
- [`weave/`](./weave/README.md): Weave Net DaemonSet installation and troubleshooting.

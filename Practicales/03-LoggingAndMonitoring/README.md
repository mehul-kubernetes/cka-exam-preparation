# 03 - Logging and Monitoring

## 📌 Overview
The **Logging and Monitoring** domain tests your ability to inspect the health, metrics, and runtime log streams of containers, pods, and cluster system services.

---

## 🔍 Pod & Container Logging (`kubectl logs`)

```bash
# 1. View logs of a single container pod
kubectl logs <pod-name>

# 2. Follow logs live (-f)
kubectl logs -f <pod-name>

# 3. View logs of a specific container in a multi-container pod (-c)
kubectl logs <pod-name> -c <container-name>

# 4. View logs of a PREVIOUS instance of a crashed container (-p) [CRUCIAL FOR TROUBLESHOOTING!]
kubectl logs -p <pod-name>
kubectl logs --previous <pod-name> -c <container-name>

# 5. Tail last N lines
kubectl logs --tail=50 <pod-name>

# 6. Stream logs from all pods matching a label
kubectl logs -l app=nginx --tail=20
```

---

## 🖥️ Node & Service Logging (System Level)

When control-plane or worker node components fail (e.g. `kubelet` or `containerd`), inspect systemd journal logs:

```bash
# View kubelet service logs
journalctl -u kubelet -n 100 --no-pager
journalctl -u kubelet -f

# View container runtime logs
journalctl -u containerd -n 50 --no-pager

# Direct container log file paths on nodes:
# /var/log/pods/
# /var/log/containers/
```

---

## 📊 Monitoring Cluster Metrics (`kubectl top`)

Kubernetes requires the **Metrics Server** to expose resource metrics.

```bash
# View CPU and Memory usage for all Nodes
kubectl top nodes

# View CPU and Memory usage for all Pods in current namespace
kubectl top pods

# View CPU and Memory usage across ALL namespaces
kubectl top pods -A

# Sort pods by memory or CPU consumption
kubectl top pods -A --sort-by='memory'
kubectl top pods -A --sort-by='cpu'
```

---

## 💡 CKA Exam Tips
- If `kubectl top` fails with `Metrics API not available`, verify if metrics-server is running in `kube-system`:
  `kubectl get pods -n kube-system | grep metrics-server`
- Remember that `kubectl logs -p` is the first command you should run when a pod is in `CrashLoopBackOff`.

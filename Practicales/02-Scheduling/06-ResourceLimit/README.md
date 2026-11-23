# 06 - Resource Requests and Limits

## 📌 Overview
When you specify a Pod, you can optionally specify how much CPU and memory (RAM) each container needs:
- **Requests**: Minimum guaranteed resources needed for scheduling. The scheduler places a Pod only on a node with enough allocatable capacity.
- **Limits**: Maximum ceiling resources a container is permitted to consume.

---

## 🔑 Key Concepts & Units
- **CPU**: Measured in millicores / millicpus. `1000m` = `1 vCPU / Core`. `500m` = `0.5 vCPU`.
  - CPU is a **compressible** resource: If a container exceeds its limit, Kubernetes throttles the CPU.
- **Memory**: Measured in bytes (typically `Mi` mebibytes or `Gi` gibibytes).
  - Memory is a **non-compressible** resource: If a container attempts to allocate more memory than its limit, the container is killed by the kernel with **`OOMKilled` (Exit Code 137)**.

---

## 📄 Manifest Example: `resource-demo.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: app
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

---

## 🛠️ Practical Commands & Verification

```bash
# Check resource consumption across nodes
kubectl top nodes

# Check resource consumption across pods
kubectl top pods
kubectl top pods -A --sort-by='memory'

# Check why a pod is failing with Exit Code 137
kubectl describe pod <pod-name>
# Look for: Last State: Terminated, Reason: OOMKilled, Exit Code: 137
```

---

## 💡 CKA Exam Tips
- If an exam question asks: *"Create a pod with 0.5 CPU and 256Mi memory"*, set:
  ```yaml
  resources:
    requests:
      cpu: "500m"
      memory: "256Mi"
  ```
- **LimitRange**: Sets default requests/limits for pods in a namespace that don't explicitly define them.
- **ResourceQuota**: Hard ceiling on total aggregate CPU/memory that can be requested in a namespace.

# 01 - Single Container Pods, Commands, Arguments & Lifecycles

## 📌 Overview
This directory demonstrates single-container Pod specifications, command/argument overriding, exit codes, container logging, and Pod lifecycle states.

---

## 🔑 Docker vs Kubernetes: Command and Arguments

In Kubernetes:
- **`command`** in Kubernetes overrides the container's **`ENTRYPOINT`** in Docker.
- **`args`** in Kubernetes overrides the container's **`CMD`** in Docker.

| Manifest Field | Docker Equivalent | Description |
|---|---|---|
| `spec.containers[].command` | `ENTRYPOINT` | The executable program to run. |
| `spec.containers[].args` | `CMD` | The default arguments passed to the executable. |

---

## 📄 Manifest Files in this Directory

| File Name | Purpose / Topic | Key Spec Fields |
|---|---|---|
| `pod-definition.yml` | Base single-container Pod | Basic Nginx container listening on port 80. |
| `pod-arguments-1.yaml` | Overriding executable and arguments | `command: ["echo"]`, `args: ["Hello Word!"]`, `restartPolicy: Never`. |
| `pod-arguments-2.yaml` | Passing inline shell commands | `command: ["/bin/sh", "-c"]`, `args: ["echo Hello Kubernetes"]`. |
| `pod-lifecycle-running.yaml` | Continuously running Pod | Long-running process (`sleep 3600`) with `restartPolicy: Always`. |
| `pod-lifecycle-completed.yaml` | Run-to-completion Pod (Exit 0) | `echo done`, terminates successfully with `STATUS: Completed` (`restartPolicy: Never`). |
| `pod-lifecycle-fail.yaml` | Errored / Failed Pod (Exit non-zero) | Process exits with non-zero exit code (`exit 1`), resulting in `STATUS: Error`. |
| `pod-lifecycle-crashloopbackoff.yaml` | CrashLoopBackOff Simulation | A terminating command (`echo done`) combined with `restartPolicy: Always`. Kubelet keeps restarting it with exponential backoff delay (10s, 20s, 40s...). |
| `pod-lifecycle-logs.yaml` | Static log extraction | Container prints multiple messages to stdout for inspection. |
| `pod-lifecycle-livelogs.yaml` | Live streaming log generation | Continuous while-loop generating timestamped logs. |

---

## 🛠️ Step-by-Step Hands-On Exercises

### 1. Test Command & Arguments Override
```bash
# Apply pod with custom arguments
kubectl apply -f pod-arguments-1.yaml

# Verify output in logs
kubectl logs pod-arguments
# Output: Hello Word!

# Check pod status (Completed because restartPolicy: Never)
kubectl get pod pod-arguments
```

### 2. Understand CrashLoopBackOff
```bash
# Deploy pod that exits immediately with restartPolicy: Always
kubectl apply -f pod-lifecycle-crashloopbackoff.yaml

# Watch pod status changes in real time
kubectl get pod pod-lifecycle-crashloopbackoff -w
# Lifecycle progression:
# ContainerCreating -> Running -> Completed -> CrashLoopBackOff
```

### 3. Inspect Previous Crashed Container Logs (Vital for CKA Troubleshooting!)
```bash
# View current logs
kubectl logs pod-lifecycle-crashloopbackoff

# View PREVIOUS terminated container instance logs:
kubectl logs pod-lifecycle-crashloopbackoff --previous
# or
kubectl logs pod-lifecycle-crashloopbackoff -p
```

### 4. Live Log Streaming
```bash
kubectl apply -f pod-lifecycle-livelogs.yaml

# Follow log output live (-f):
kubectl logs -f pod-lifecycle-livelogs

# Tail only the last 10 lines:
kubectl logs --tail=10 pod-lifecycle-livelogs
```

### 5. Cleanup
```bash
kubectl delete pod --all
```

---

## 💡 CKA Exam Tips
- If a pod is in `CrashLoopBackOff`, run `kubectl describe pod <name>` and look at `Last State: Terminated` for the **Exit Code** and **Reason**.
  - `Exit Code 0`: The container finished its job, but `restartPolicy: Always` forced kubelet to restart it. Use a Job/CronJob or set `restartPolicy: OnFailure / Never`.
  - `Exit Code 1`: Application crashed / threw an unhandled exception.
  - `Exit Code 137`: Container was killed by the kernel due to out-of-memory (`OOMKilled`).
  - `Exit Code 143`: Container received SIGTERM signal.

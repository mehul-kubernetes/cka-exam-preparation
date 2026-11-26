# 06 - Pod Lifecycle, Phases & Diagnosing States

## 📌 Overview
A Kubernetes Pod follows a defined lifecycle starting from **`Pending`**, moving through **`Running`**, and terminating as either **`Succeeded`** or **`Failed`**.

Understanding the difference between **Pod Phases**, **Container States**, and **Pod Conditions** is fundamental for troubleshooting questions in the **CKA Exam** (30% weightage).

---

## 🧭 Pod Phases vs Container States

### 1. High-Level Pod Phases (`pod.status.phase`)
- **`Pending`**: The Pod has been accepted by the cluster, but one or more containers have not been scheduled or are still downloading images (`ContainerCreating`).
- **`Running`**: The Pod has been bound to a Node, and all containers have been created. At least one container is currently running or in the process of starting.
- **`Succeeded`**: All containers in the Pod have terminated successfully (Exit Code `0`) and will not be restarted.
- **`Failed`**: All containers in the Pod have terminated, and at least one container has terminated in failure (Exit Code non-zero).
- **`Unknown`**: State cannot be obtained, usually due to a communication failure between the control plane and the worker node `kubelet`.

### 2. Low-Level Container States (`containerStatuses[].state`)
- **`Waiting`**: Container is waiting to run (e.g. `ContainerCreating`, `ImagePullBackOff`, `CrashLoopBackOff`).
- **`Running`**: Container process is actively running without issues.
- **`Terminated`**: Container process executed and completed (e.g. `Completed`, `Error`, `OOMKilled`).

---

## 📄 Manifest Files in this Directory

| File Name | Configured Scenario | Resulting Status |
|---|---|---|
| `pod-lifecycle-running.yaml` | Standard web server (`nginx`), `restartPolicy: Always`. | `Running` |
| `pod-lifecycle-completed.yaml` | Task exits with code 0, `restartPolicy: Never`. | `Completed` (`Succeeded`) |
| `pod-lifecycle-fail.yaml` | Task exits with non-zero code, `restartPolicy: Never`. | `Error` (`Failed`) |
| `pod-lifecycle-crashloopbackoff.yaml` | Terminating command (`echo done`), `restartPolicy: Always`. | `CrashLoopBackOff` |

---

## 🛠️ Step-by-Step Practical Exercises

### 1. Observe a Successfully Completed Pod
```bash
kubectl apply -f pod-lifecycle-completed.yaml
kubectl get pod pod-lifecycle-completed
# Output:
# NAME                      READY   STATUS      RESTARTS   AGE
# pod-lifecycle-completed   0/1     Completed   0          10s
```

### 2. Observe a Failed Container
```bash
kubectl apply -f pod-lifecycle-fail.yaml
kubectl get pod pod-lifecycle-fail
# Output:
# NAME                 READY   STATUS   RESTARTS   AGE
# pod-lifecycle-fail   0/1     Error    0          8s
```

### 3. Observe CrashLoopBackOff Progression
```bash
kubectl apply -f pod-lifecycle-crashloopbackoff.yaml

# Watch the status transitions live:
kubectl get pod pod-lifecycle-crashloopbackoff -w
# Lifecycle:
# ContainerCreating -> Running -> Completed -> CrashLoopBackOff
```

### 4. How to Troubleshoot Failed / Crashed Pods
Whenever a pod is not running as expected, run this standard 3-step diagnostic checklist:

```bash
# Step 1: Check Pod Events and Last Termination State
kubectl describe pod <pod-name>

# Step 2: Read Current Logs
kubectl logs <pod-name>

# Step 3: Read PREVIOUS Instance Logs (If container restarted)
kubectl logs <pod-name> --previous
```

### 5. Cleanup
```bash
kubectl delete pod --all
```

---

## 💡 CKA Exit Code Cheat Sheet

| Exit Code | Meaning | Common Cause |
|---|---|---|
| **`0`** | Success | Container finished its commanded task cleanly. |
| **`1`** | General Error | Application code crashed, script exited with error, or missing config. |
| **`127`** | Command Not Found | The `command` specified in the manifest does not exist in the container image. |
| **`137`** | SIGKILL / OOMKilled | Container exceeded memory limits (`OOMKilled`) or was forcefully killed (`kill -9`). |
| **`143`** | SIGTERM | Container received graceful termination signal from Kubernetes. |

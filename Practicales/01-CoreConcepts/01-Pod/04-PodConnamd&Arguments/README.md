# 04 - Pod Commands and Arguments

## 📌 Overview
In Kubernetes, you can customize the default executable and parameters of any container image using **`command`** and **`args`**.

Understanding how these fields map to Docker instructions is a frequent topic in the **CKA Exam**:

| Manifest Field | Docker Equivalent | Description |
|---|---|---|
| **`spec.containers[].command`** | `ENTRYPOINT` | The executable program to run. |
| **`spec.containers[].args`** | `CMD` | The default arguments passed to the executable program. |

---

## 🔄 Interaction Matrix: Kubernetes vs Docker

| In Docker Image | In Pod Manifest | Resulting Container Execution |
|---|---|---|
| `ENTRYPOINT`, `CMD` | *(Neither set)* | Uses Docker image `ENTRYPOINT` with default `CMD`. |
| `ENTRYPOINT`, `CMD` | `command` set, `args` not set | Runs custom `command`. Docker `ENTRYPOINT` and `CMD` are **both ignored**. |
| `ENTRYPOINT`, `CMD` | `args` set, `command` not set | Runs Docker image `ENTRYPOINT` with your custom `args`. Docker `CMD` is ignored. |
| `ENTRYPOINT`, `CMD` | Both `command` & `args` set | Runs custom `command` with custom `args`. Docker image `ENTRYPOINT` and `CMD` are **both ignored**. |

---

## 📄 Manifest Files in this Directory

| File Name | Purpose | Key Spec Fields |
|---|---|---|
| `pod-arguments-1.yaml` | Overriding executable and arguments | `command: ["echo"]`, `args: ["Hello Word!"]`, `restartPolicy: Never`. |
| `pod-arguments-2.yaml` | Passing arguments to long-running process | `command: ["sleep"]`, `args: ["3600"]`, `restartPolicy: Never`. |
| `pod-command.yaml` | Running inline shell script with exit code | `command: ["sh", "-c", "echo task failed; exit 1"]`, `restartPolicy: Never`. |

---

## 🚀 CKA Fast Imperative Commands

Save time during the exam by passing commands and arguments directly to `kubectl run`:

```bash
# 1. Passing args after '--' (overrides CMD)
kubectl run my-pod --image=busybox --restart=Never -- echo "Hello CKA"

# 2. Passing custom command with '--command --' (overrides ENTRYPOINT)
kubectl run my-pod --image=busybox --restart=Never --command -- env

# 3. Running multi-statement shell commands
kubectl run my-pod --image=busybox --restart=Never -- sh -c "echo step1 && echo step2"

# 4. Generate YAML template with command & args
kubectl run my-pod --image=busybox --restart=Never --dry-run=client -o yaml -- sh -c "sleep 3600" > pod-cmd.yaml
```

---

## 🛠️ Step-by-Step Hands-On Exercises

### 1. Execute Custom Command with Arguments
```bash
# Deploy pod-arguments-1
kubectl apply -f pod-arguments-1.yaml

# Inspect container output
kubectl logs pod-arguments
# Output: Hello Word!

# Verify pod completed successfully
kubectl get pod pod-arguments
# STATUS: Completed
```

### 2. Inspect Non-Zero Exit Code Handling
```bash
# Deploy pod-command (exits with error code 1)
kubectl apply -f pod-command.yaml

# Check pod status
kubectl get pod pod-command
# STATUS: Error

# Check exit code and termination reason
kubectl describe pod pod-command | grep -A 4 "Last State"
# Reason: Error, Exit Code: 1
```

### 3. Cleanup
```bash
kubectl delete -f pod-arguments-1.yaml
kubectl delete -f pod-command.yaml
```

---

## 💡 CKA Exam Tips & Common Gotchas
- **Array Syntax**: In YAML, you can write commands in array format `command: ["sh", "-c", "echo hi"]` or bullet format:
  ```yaml
  command:
  - sh
  - -c
  - echo hi
  ```
- **Shell Operators**: Commands like `&&`, `||`, `|`, and `;` require a shell to evaluate. Running `command: ["echo", "a", "&&", "echo", "b"]` will print `a && echo b` literally. You must wrap them with `["sh", "-c", "..."]`.

# 05 - Pod Restart Policies (`restartPolicy`)

## 📌 Overview
The **`restartPolicy`** in a Pod specification defines how the `kubelet` handles containers after their process terminates or exits.

The restart policy applies to **all containers** within the Pod. In Kubernetes, the default restart policy is **`Always`**.

---

## 🔑 The Three Restart Policies

| Policy | Behavior | When Container Exits 0 (Success) | When Container Exits Non-Zero (Failure) | Typical Workloads |
|---|---|---|---|---|
| **`Always`** *(Default)* | Kubelet **always** restarts the container when it exits, regardless of exit status. | Restarts container | Restarts container | Deployments, ReplicaSets, StatefulSets, DaemonSets |
| **`OnFailure`** | Kubelet restarts the container **only if** the container process fails (exits with non-zero exit code). | Marks as `Completed` (No restart) | Restarts container | Batch Jobs, CronJobs, database migration scripts |
| **`Never`** | Kubelet **never** restarts the container under any circumstance. | Marks as `Completed` | Marks as `Error` | One-off debugging tasks, testing scripts |

---

## ⏱️ Exponential Back-Off Delay & `CrashLoopBackOff`

When a container fails or repeatedly exits, the `kubelet` restarts it using an **exponential back-off delay**:
$$10\text{s} \to 20\text{s} \to 40\text{s} \to 80\text{s} \to 160\text{s} \to \text{capped at } 300\text{s (5 minutes)}$$

During this back-off delay, `kubectl get pods` displays the status: **`CrashLoopBackOff`**.

> [!NOTE]
> Once a container runs continuously and successfully for **10 minutes**, the kubelet resets the backoff timer to 0.

---

## 📄 Manifest Examples

### 1. `restartPolicy: Always` (Default)
Useful for long-running web servers or background daemons:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-restart-always
spec:
  restartPolicy: Always
  containers:
  - name: web
    image: nginx
    ports:
    - containerPort: 80
```

### 2. `restartPolicy: OnFailure`
Useful for data processing tasks where success should terminate, but errors should retry:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-restart-onfailure
spec:
  restartPolicy: OnFailure
  containers:
  - name: processor
    image: busybox
    command: ["sh", "-c"]
    args:
    - |
      if [ -f /tmp/done ]; then
        echo "Job already finished successfully";
        exit 0;
      else
        echo "Encountered transient error, retrying...";
        touch /tmp/done;
        exit 1;
      fi
```

### 3. `restartPolicy: Never`
Useful for one-off debug jobs or diagnostic commands:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-restart-never
spec:
  restartPolicy: Never
  containers:
  - name: date-printer
    image: busybox
    command: ["date"]
```

---

## 🛠️ Step-by-Step Hands-On Exercise

### 1. Test `restartPolicy: Never` (Successful Completion)
```bash
# Run a one-off pod that prints date and exits 0
kubectl run one-shot-pod --image=busybox --restart=Never -- date

# Verify status transitions to Completed
kubectl get pod one-shot-pod
# STATUS: Completed, RESTARTS: 0

# Check logs
kubectl logs one-shot-pod
```

### 2. Test `restartPolicy: Always` Causing CrashLoopBackOff
If a container executes a non-looping command with `restartPolicy: Always`, Kubelet restarts it endlessly:
```bash
kubectl run loop-crash --image=busybox --restart=Always -- sh -c 'echo finished; exit 0'

# Watch status progression:
kubectl get pod loop-crash -w
# Running -> Completed -> CrashLoopBackOff (RESTARTS increments: 1, 2, 3...)
```

### 3. Inspect Previous Crashed Instance Logs (Essential for CKA!)
When troubleshooting a container in `CrashLoopBackOff`:
```bash
# View the logs from the previous failed run:
kubectl logs loop-crash --previous
# or
kubectl logs loop-crash -p

# Inspect the Last State and Exit Code:
kubectl describe pod loop-crash | grep -A 5 "Last State"
```

### 4. Cleanup
```bash
kubectl delete pod one-shot-pod loop-crash
```

---

## 💡 CKA Exam Tips & Workload Controller Restrictions
- **Deployments only accept `restartPolicy: Always`**: If you create a Deployment YAML and set `restartPolicy: OnFailure` or `Never`, the Kubernetes API server will reject it with a validation error.
- **Jobs & CronJobs only accept `restartPolicy: OnFailure` or `Never`**: A Kubernetes `Job` will reject `restartPolicy: Always` because jobs are designed to run to completion, not run infinitely.

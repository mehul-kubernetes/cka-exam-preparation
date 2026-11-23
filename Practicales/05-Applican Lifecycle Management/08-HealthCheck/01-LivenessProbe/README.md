# 01 - Liveness Probes

## 📌 Overview
A **Liveness Probe** determines whether a container is still running properly. If the liveness probe fails, the `kubelet` automatically kills the container, and the container is subjected to its `restartPolicy` (e.g., `Always` or `OnFailure`).

### When to Use:
- Catching application **deadlocks** where the process continues running (process ID exists) but cannot respond to requests.
- Recovering from fatal application memory leaks or frozen event loops without human intervention.

---

## 📄 Manifest Examples

### 1. `exec` Liveness Probe (`exec-liveness.yaml`)
In this example, the container creates a file `/tmp/healthy` on startup and deletes it after 30 seconds. The liveness probe verifies the file exists. When deleted, the probe fails and kubelet restarts the container.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-exec-pod
  labels:
    test: liveness
spec:
  containers:
  - name: liveness-container
    image: busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
      failureThreshold: 2
```

### 2. `httpGet` Liveness Probe (`http-liveness.yaml`)
Performs an HTTP GET request to `/healthz` on port 8080:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-http-pod
spec:
  containers:
  - name: web-app
    image: registry.k8s.io/liveness
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
```

### 3. `tcpSocket` Liveness Probe (`tcp-liveness.yaml`)
Attempts to establish a TCP socket on port 80:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-tcp-pod
spec:
  containers:
  - name: nginx
    image: nginx
    livenessProbe:
      tcpSocket:
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10
```

---

## 🛠️ Step-by-Step Practical & Verification

1. **Deploy Exec Liveness Pod**:
   ```bash
   kubectl apply -f exec-liveness.yaml
   ```

2. **Watch the Pod Status & Restarts**:
   ```bash
   kubectl get pod liveness-exec-pod -w
   ```
   *Observation*:
   - First 30 seconds: `STATUS: Running`, `RESTARTS: 0`.
   - After ~35 seconds: The file `/tmp/healthy` is deleted. The probe fails twice (`failureThreshold: 2`).
   - Notice `RESTARTS` increments to `1`!

3. **Inspect Failure Events**:
   ```bash
   kubectl describe pod liveness-exec-pod
   ```
   Look at the **Events** section at the bottom:
   ```text
   Warning  Unhealthy  Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory
   Normal   Killing    Container liveness-container failed liveness probe, will be restarted
   ```

4. **Clean up**:
   ```bash
   kubectl delete pod liveness-exec-pod
   ```

---

## 💡 CKA Exam Tips & Gotchas
- **Do NOT confuse with Readiness Probe**: A liveness probe **kills and restarts** the container. It does NOT toggle Service Endpoints.
- **Set realistic `initialDelaySeconds`**: If your app takes 10 seconds to warm up and `initialDelaySeconds` is set to 2, Kubernetes will kill the container before it has a chance to boot!

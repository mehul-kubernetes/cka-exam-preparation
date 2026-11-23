# 03 - Startup Probes

## 📌 Overview
A **Startup Probe** determines whether the application within a container has successfully started.

### The Problem It Solves:
Many legacy applications (e.g. Java Spring Boot, large database engines) require significant time to initialize (30–120+ seconds) on initial boot.
- If you only configure a **Liveness Probe**, the container may be killed and restarted before it finishes booting, entering a perpetual crash-restart loop.
- If you increase `initialDelaySeconds` on a Liveness Probe to compensate, your application will take a very long time to detect and recover from runtime deadlocks later.

### How Startup Probes Work:
- All other probes (**Liveness and Readiness**) are **disabled** until the Startup Probe succeeds.
- If the Startup Probe never succeeds within its configured time window, the container is killed and restarted.
- Once the Startup Probe succeeds once, it **never runs again** for the life of that container; Liveness and Readiness probes take over normal monitoring.

---

## 📄 Manifest Example: `startup-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: slow-boot-pod
spec:
  containers:
  - name: legacy-app
    image: busybox
    args:
    - /bin/sh
    - -c
    - sleep 45; touch /tmp/started; sleep 3600
    # Startup probe gives the app up to: 30 * 2s = 60s to finish booting
    startupProbe:
      exec:
        command:
        - cat
        - /tmp/started
      initialDelaySeconds: 5
      periodSeconds: 2
      failureThreshold: 30

    # Liveness probe only takes over AFTER startup probe passes
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/started
      periodSeconds: 5
      failureThreshold: 2
```

---

## ⏱️ Calculating Maximum Startup Time

The maximum allowable startup time is calculated as:
$$\text{Max Startup Time} = \text{failureThreshold} \times \text{periodSeconds}$$

In the manifest above:
$$30 \times 2\text{s} = 60\text{ seconds}$$

This guarantees the slow application has up to 60 seconds to boot before Kubernetes takes any restart action, while keeping the Liveness Probe fast and responsive (`periodSeconds: 5`) once running!

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy Pod**:
   ```bash
   kubectl apply -f startup-pod.yaml
   ```

2. **Observe Startup Phase**:
   ```bash
   kubectl get pod slow-boot-pod -w
   ```
   *Notice*:
   - The container sleeps for 45s before creating `/tmp/started`.
   - The startup probe fails up to ~20 times without killing the container.
   - At second 45, `/tmp/started` appears: Startup probe succeeds $\to$ Liveness probe activates $\to$ Status becomes `Running (1/1)`.

3. **Clean up**:
   ```bash
   kubectl delete pod slow-boot-pod
   ```

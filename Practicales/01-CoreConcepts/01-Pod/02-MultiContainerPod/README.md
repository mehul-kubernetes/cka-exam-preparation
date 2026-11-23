# Multi-Container Pod Design Patterns

## 📌 Overview
In Kubernetes, containers in a **Multi-Container Pod** share:
- **Network Namespace**: They can communicate via `localhost` and share ports.
- **Storage Volumes**: They can share filesystem storage using an `emptyDir` or shared persistent volume.
- **Lifecycle**: They are scheduled, started, and stopped together on the exact same worker node.

### Common Multi-Container Patterns (Tested in CKA):
1. **Sidecar**: Enhances or extends the main container (e.g., log forwarding agent, proxy).
2. **Adapter**: Standardizes the output or protocol of the main container (e.g., formatting custom metrics for Prometheus).
3. **Ambassador**: Acts as a proxy connecting the main container to external services (e.g., database proxy, redis sentinel).

---

## 🚀 Example Manifest: Sidecar / Multi-Container Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-demo
  labels:
    app: multi-app
spec:
  volumes:
  - name: shared-logs
    emptyDir: {}
  containers:
  # Main application container
  - name: app-container
    image: alpine
    command: ["/bin/sh", "-c"]
    args:
      - while true; do
          date >> /var/log/app.log;
          sleep 2;
        done
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log

  # Sidecar / Logging container reading shared logs
  - name: log-sidecar
    image: busybox
    command: ["/bin/sh", "-c"]
    args:
      - tail -f /var/log/app.log
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log
```

---

## 🛠️ Step-by-Step Practical & Verification

1. **Deploy the Multi-Container Pod**:
   ```bash
   kubectl apply -f multi-container-demo.yaml
   ```

2. **Verify Pod Status**:
   ```bash
   kubectl get pod multi-container-demo
   # Look for READY 2/2
   ```

3. **Check Logs of Specific Containers**:
   ```bash
   # Read logs from the sidecar container
   kubectl logs multi-container-demo -c log-sidecar

   # Follow logs live
   kubectl logs multi-container-demo -c log-sidecar -f
   ```

4. **Execute Commands in a Specific Container**:
   ```bash
   kubectl exec -it multi-container-demo -c app-container -- ls -la /var/log
   ```

5. **Clean up**:
   ```bash
   kubectl delete pod multi-container-demo
   ```

---

## 💡 CKA Exam Tips
- Both containers cannot bind to the **same port** on `localhost`, or one will fail to start.
- When generating Pods, use `kubectl run <pod-name> --image=<image> --dry-run=client -o yaml > pod.yaml` and add the second container under `spec.containers`.

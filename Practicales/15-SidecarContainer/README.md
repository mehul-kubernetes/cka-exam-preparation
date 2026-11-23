# 15 - Sidecar Containers

## 📌 Overview
A **Sidecar Container** is a secondary container that runs alongside the primary application container within the same Pod. Its purpose is to augment, enhance, or extend the functionality of the main container without altering the main container's code.

### Common Use Cases:
- **Log Collection / Aggregation**: Streaming or shipping log files produced by the application container.
- **Monitoring & Metrics**: Exposing Prometheus metrics from legacy applications.
- **Network Proxy / Service Mesh**: Routing traffic (e.g., Envoy sidecar in Istio).
- **Configuration Reloading**: Watching external secrets/configs and updating local volumes.

---

## 📄 Manifest Example: `sidecar.yaml`

In this example, an Nginx container generates access logs in `/var/log/nginx`, while a busybox sidecar continuously tails and streams the logs to stdout.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-pod
  labels:
    app: sidecar-demo
spec:
  volumes:
  - name: shared-logs
    emptyDir: {}
  containers:
  # Primary application container
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx

  # Sidecar container to stream logs
  - name: log-sidecar
    image: busybox
    command: ["/bin/sh", "-c"]
    args:
    - while true; do
        if [ -f /var/log/nginx/access.log ]; then
          tail -n+1 -f /var/log/nginx/access.log;
        fi;
        sleep 1;
      done
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx
```

---

## 🚀 Native Sidecar Containers (Kubernetes 1.28+)

Kubernetes 1.28+ introduced native Sidecar support using `initContainers` with `restartPolicy: Always`:
```yaml
spec:
  initContainers:
  - name: native-sidecar
    image: fluent/fluent-bit
    restartPolicy: Always    # Keeps running throughout the pod lifecycle
  containers:
  - name: app
    image: my-app
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy Pod**:
   ```bash
   kubectl apply -f sidecar.yaml
   ```

2. **Verify Both Containers Running**:
   ```bash
   kubectl get pod sidecar-pod
   # READY should show 2/2
   ```

3. **Generate Traffic to Nginx**:
   ```bash
   kubectl exec -it sidecar-pod -c nginx -- curl -s http://localhost
   ```

4. **Verify Logs Captured by Sidecar**:
   ```bash
   kubectl logs sidecar-pod -c log-sidecar
   ```

5. **Clean up**:
   ```bash
   kubectl delete -f sidecar.yaml
   ```

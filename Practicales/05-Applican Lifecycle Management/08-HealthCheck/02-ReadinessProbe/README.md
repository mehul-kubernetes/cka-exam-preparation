# 02 - Readiness Probes

## 📌 Overview
A **Readiness Probe** determines whether a container is ready to start accepting network traffic.

### Crucial Distinction:
Unlike a Liveness Probe, **a failing Readiness Probe NEVER kills or restarts the container**. Instead:
- If a container's readiness probe fails, the Kubernetes Endpoints controller **removes the Pod's IP address from all matching Services**.
- When the readiness probe succeeds again, the Pod's IP is automatically added back to the Service Endpoints.

---

## 📄 Manifest Example: `readiness-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readiness-demo-pod
  labels:
    app: web-service
spec:
  containers:
  - name: web
    image: nginx
    ports:
    - containerPort: 80
    readinessProbe:
      httpGet:
        path: /ready.html
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
      failureThreshold: 1
```

---

## 🛠️ Step-by-Step Hands-On Exercise: Service Endpoints Coupling

1. **Create a Service targeting the Pod**:
   ```bash
   kubectl create service clusterip web-service --tcp=80:80
   ```

2. **Deploy the Pod with Readiness Probe**:
   ```bash
   kubectl apply -f readiness-pod.yaml
   ```

3. **Check Pod Readiness Status**:
   ```bash
   kubectl get pods -l app=web-service
   ```
   *Notice*:
   - `STATUS` is `Running`, but `READY` shows `0/1` because `/ready.html` does not exist yet!

4. **Inspect Service Endpoints**:
   ```bash
   kubectl get endpoints web-service
   ```
   *Notice*:
   - `ENDPOINTS` is `<none>`. No traffic is routed to this pod!

5. **Make the Pod Ready**:
   Exec into the container and create the `/ready.html` file:
   ```bash
   kubectl exec readiness-demo-pod -- touch /usr/share/nginx/html/ready.html
   ```

6. **Verify Endpoints Updated**:
   ```bash
   kubectl get pods -l app=web-service
   # READY now transitions to: 1/1

   kubectl get endpoints web-service
   # ENDPOINTS now lists the Pod IP (e.g., 10.244.1.25:80)!
   ```

7. **Simulate Outage / Unreadiness**:
   ```bash
   kubectl exec readiness-demo-pod -- rm /usr/share/nginx/html/ready.html
   kubectl get pods -l app=web-service
   # READY reverts to: 0/1 (Container is STILL RUNNING, NOT restarted!)
   kubectl get endpoints web-service
   # ENDPOINTS becomes <none> again.
   ```

8. **Clean up**:
   ```bash
   kubectl delete pod readiness-demo-pod
   kubectl delete service web-service
   ```

---

## 💡 CKA Exam Tips
- Exam questions often ask: *"Configure a readiness probe on deployment `web` checking HTTP path `/health` on port 8080 with an initial delay of 10s and period of 5s"*.
- When troubleshooting why a Service is returning `502 Bad Gateway` or `Connection Refused`, always run `kubectl get ep <service-name>`:
  - If `ENDPOINTS` is `<none>`, inspect `kubectl describe pod` for readiness probe failures!

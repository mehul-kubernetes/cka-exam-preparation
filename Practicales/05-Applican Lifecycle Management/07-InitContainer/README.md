# 16 - InitContainers

## 📌 Overview
**InitContainers** are specialized containers that run before application containers in a Pod. They contain utilities or setup scripts not present in an app image.

### Key Rules of InitContainers:
1. They **always run to completion**.
2. Each init container must exit successfully (`0`) before the next one starts.
3. If an init container fails, Kubernetes restarts the Pod until the init container succeeds (unless Pod `restartPolicy: Never`).
4. Regular application containers do **not** start until all init containers complete.

---

## 📄 Manifest Example: `init-container.yaml`

In this example, the InitContainer waits for a database service `db-service` to become resolvable/reachable via DNS before starting the web application container.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  initContainers:
  - name: wait-for-db
    image: busybox:1.28
    command: ['sh', '-c', 'until nc -z -w 2 db-service 3306; do echo waiting for db; sleep 2; done;']
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy Pod**:
   ```bash
   kubectl apply -f init-container.yaml
   ```

2. **Observe Status**:
   ```bash
   kubectl get pod myapp-pod
   # Status will show: Init:0/1
   ```

3. **Check InitContainer Logs**:
   ```bash
   kubectl logs myapp-pod -c wait-for-db
   ```

4. **Simulate Service Availability**:
   ```bash
   # Create dummy service and endpoint to satisfy init check
   kubectl create service clusterip db-service --tcp=3306:3306
   ```

5. **Observe App Transition to Running**:
   ```bash
   kubectl get pod myapp-pod -w
   # Status changes: Init:0/1 -> PodInitializing -> Running (READY: 1/1)
   ```

6. **Clean up**:
   ```bash
   kubectl delete pod myapp-pod
   kubectl delete service db-service
   ```

---

## 💡 CKA Exam Tips
- Use `kubectl describe pod <pod-name>` and look at `Init Containers:` section to see state (`Terminated`, `Exit Code: 0`, `Reason: Completed`).
- Init containers do not support `readinessProbe` or `livenessProbe` because they must run to completion before the pod can become ready.

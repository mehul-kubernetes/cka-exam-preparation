# 03 - Configuring Environment Variables & Downward API

## 📌 Overview
Kubernetes allows you to inject environment variables into containers using:
1. **Plain Key-Value Pairs** (`env.value`)
2. **ConfigMaps & Secrets** (`configMapKeyRef`, `secretKeyRef`)
3. **Downward API (`fieldRef`)**: Exposing Pod and Node metadata to the container as environment variables.
4. **Resource Quota Fields (`resourceFieldRef`)**: Exposing container CPU/memory requests and limits.

---

## 📄 Manifest Example: Comprehensive Variable Configuration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: env-demo-pod
  labels:
    zone: us-east
spec:
  containers:
  - name: app
    image: busybox:1.28
    command: ["sh", "-c", "env && sleep 3600"]
    env:
    # 1. Plain static variable
    - name: APP_ENVIRONMENT
      value: "production"

    # 2. Downward API: Pod Name
    - name: MY_POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name

    # 3. Downward API: Pod Namespace
    - name: MY_POD_NAMESPACE
      valueFrom:
        fieldRef:
          fieldPath: metadata.namespace

    # 4. Downward API: Pod IP Address
    - name: MY_POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP

    # 5. Downward API: Worker Node Name
    - name: MY_NODE_NAME
      valueFrom:
        fieldRef:
          fieldPath: spec.nodeName

    # 6. Container Memory Limit via resourceFieldRef
    - name: MY_MEM_LIMIT
      valueFrom:
        resourceFieldRef:
          containerName: app
          resource: limits.memory
    resources:
      limits:
        memory: "128Mi"
```

---

## 🛠️ Verification
```bash
# Apply pod
kubectl apply -f env-demo-pod.yaml

# Inspect environment variables injected inside the container
kubectl logs env-demo-pod | grep MY_
# Output:
# MY_POD_NAME=env-demo-pod
# MY_POD_NAMESPACE=default
# MY_POD_IP=10.244.1.15
# MY_NODE_NAME=node01
# MY_MEM_LIMIT=134217728
```

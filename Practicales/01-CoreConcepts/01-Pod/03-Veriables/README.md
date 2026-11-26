# 03 - Environment Variables in Pods

## 📌 Overview
Environment variables allow you to customize container behavior without changing the underlying container image. In Kubernetes, environment variables can be injected into containers using:
1. **Static Plain Key-Value Pairs** (`env.name` and `env.value`)
2. **References to ConfigMaps** (`configMapKeyRef` / `envFrom.configMapRef`)
3. **References to Secrets** (`secretKeyRef` / `envFrom.secretRef`)
4. **Downward API** (`fieldRef` and `resourceFieldRef`)

---

## 📄 Manifest Example: `pod-veriables.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-veriables
spec:
  containers:
    - name: demo-container
      image: busybox
      command: ["sleep", "3600"]
      env:
        - name: UI_COLOR
          value: blue
        - name: CUSTOMER_NAME
          value: customer-a
```

---

## 🚀 CKA Fast Imperative Commands

```bash
# Generate a pod with environment variables directly from CLI
kubectl run my-env-pod --image=nginx --env="APP_ENV=prod" --env="DB_PORT=3306"

# Generate YAML without creating:
kubectl run my-env-pod --image=nginx --env="APP_ENV=prod" --dry-run=client -o yaml > pod-env.yaml
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy the Pod**:
   ```bash
   kubectl apply -f pod-veriables.yaml
   ```

2. **Verify Pod Status**:
   ```bash
   kubectl get pod pod-veriables
   ```

3. **Verify Environment Variables Inside the Running Container**:
   ```bash
   kubectl exec pod-veriables -- env
   # Output will include:
   # UI_COLOR=blue
   # CUSTOMER_NAME=customer-a

   # Print a specific variable:
   kubectl exec pod-veriables -- sh -c 'echo $UI_COLOR'
   ```

4. **Clean up**:
   ```bash
   kubectl delete pod pod-veriables
   ```

---

## 💡 CKA Exam Tips
- In multi-container pods, remember to specify the container using `-c <container-name>` when running `kubectl exec <pod> -c <container> -- env`.
- Variables defined earlier in the `env` list can be referenced by subsequent variables using `$(VAR_NAME)` syntax.

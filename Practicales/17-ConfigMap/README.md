# 17 - ConfigMaps

## 📌 Overview
A **ConfigMap** is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume.

ConfigMaps decouple environment-specific configurations from container images, making applications portable across dev, test, and prod.

---

## 🚀 CKA Fast Imperative Commands

```bash
# 1. Create ConfigMap from literal values
kubectl create configmap app-config --from-literal=APP_COLOR=blue --from-literal=APP_MODE=prod

# 2. Create ConfigMap from a file
kubectl create configmap nginx-config --from-file=default.conf

# 3. Create ConfigMap from an env file
kubectl create configmap env-config --from-env-file=.env

# 4. Generate ConfigMap YAML
kubectl create configmap app-config --from-literal=DB_PORT=3306 --dry-run=client -o yaml > config-map.yaml
```

---

## 📄 Manifest Examples

### 1. ConfigMap Manifest (`config-map.yaml`)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
```

### 2. Consuming ConfigMap via `envFrom` (Inject all keys as environment variables)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-config-map-env
spec:
  containers:
  - name: app
    image: nginx
    envFrom:
    - configMapRef:
        name: app-config
```

### 3. Consuming Single Key via `valueFrom`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-config-map-single
spec:
  containers:
  - name: app
    image: nginx
    env:
    - name: MY_COLOR
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_COLOR
```

### 4. Consuming ConfigMap as Volume Mount
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-config-map-vol
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Create ConfigMap**:
   ```bash
   kubectl apply -f config-map.yaml
   kubectl get configmaps
   kubectl describe configmap app-config
   ```

2. **Deploy Pod**:
   ```bash
   kubectl apply -f pod-config-map-env-definition.yaml
   ```

3. **Verify Environment Variables Inside Container**:
   ```bash
   kubectl exec pod-config-map-env -- env | grep APP_
   # Output:
   # APP_COLOR=blue
   # APP_MODE=prod
   ```

4. **Clean up**:
   ```bash
   kubectl delete pod pod-config-map-env
   kubectl delete configmap app-config
   ```

# 13 - PersistentVolumeClaims (PVC)

## 📌 Overview
A **PersistentVolumeClaim (PVC)** is a request for storage by a user. It is similar to a Pod: Pods consume node resources (CPU and memory), while PVCs consume PV resources (storage capacity and access modes).

Unlike PVs, PVCs are **namespaced** resources.

---

## 📄 Manifest Examples

### 1. PersistentVolumeClaim (`pvc.yaml`)
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

### 2. Pod Using PVC (`pod-pvc.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-using-pvc
spec:
  containers:
  - name: web-server
    image: nginx
    volumeMounts:
    - name: web-storage
      mountPath: /usr/share/nginx/html
  volumes:
  - name: web-storage
    persistentVolumeClaim:
      claimName: mypvc
```

### 3. Deployment Using PVC (`deploy-pvc.yaml`)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: storage
          mountPath: /usr/share/nginx/html
      volumes:
      - name: storage
        persistentVolumeClaim:
          claimName: mypvc
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Create PV & PVC**:
   ```bash
   kubectl apply -f ../12-Persistanvolume/pv.yaml
   kubectl apply -f pvc.yaml
   ```

2. **Verify Binding**:
   ```bash
   kubectl get pv
   kubectl get pvc
   # Both should now transition to status: Bound
   ```

3. **Deploy Pod and Test Persistence**:
   ```bash
   kubectl apply -f pod-pvc.yaml
   kubectl exec -it pod-using-pvc -- sh -c 'echo "CKA Storage Test" > /usr/share/nginx/html/index.html'

   # Delete pod and recreate it
   kubectl delete pod pod-using-pvc
   kubectl apply -f pod-pvc.yaml

   # Confirm file still exists
   kubectl exec -it pod-using-pvc -- cat /usr/share/nginx/html/index.html
   ```

4. **Clean up**:
   ```bash
   kubectl delete -f pod-pvc.yaml
   kubectl delete -f pvc.yaml
   ```

---

## 💡 CKA Exam Tips
- A PVC cannot be deleted if it is currently mounted by an active Pod (it will be stuck in `Terminating` until the Pod is deleted).
- Check `kubectl describe pvc <name>` if status stays `Pending` to see reasons (e.g., `no persistent volumes available for this claim and no storage class is set`).

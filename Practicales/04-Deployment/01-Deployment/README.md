# Deployment Definitions & Lifecycle

## 📌 Overview
This practical covers creating and managing standard Kubernetes Deployment manifests.

---

## 📄 Manifest Example: `deployment-definition.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: my-app
    type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      type: front-end
  template:
    metadata:
      labels:
        app: my-app
        type: front-end
    spec:
      containers:
      - name: c001
        image: nginx:1.24
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy**:
   ```bash
   kubectl apply -f deployment-definition.yml
   ```

2. **Verify Deployment, ReplicaSet, and Pods**:
   ```bash
   kubectl get deployments
   kubectl get rs
   kubectl get pods -l app=my-app -o wide
   ```

3. **Modify Image and Record**:
   ```bash
   kubectl set image deployment/my-deployment c001=nginx:1.25
   kubectl rollout status deployment/my-deployment
   ```

4. **Verify Rollout History**:
   ```bash
   kubectl rollout history deployment/my-deployment
   ```

5. **Clean up**:
   ```bash
   kubectl delete deployment my-deployment
   ```

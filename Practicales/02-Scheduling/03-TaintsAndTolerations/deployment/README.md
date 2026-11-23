# Deployments with Tolerations

## 📌 Overview
When running workloads at scale, tolerations are placed under `spec.template.spec.tolerations` of the Deployment manifest.

---

## 📄 Manifest Examples

### 1. Dev Environment Deployment (`my-deploy-dev.yaml`)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deploy-dev
  labels:
    app: my-deploy-dev
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-deploy-dev
  template:
    metadata:
      labels:
        app: my-deploy-dev
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
      tolerations:
      - key: "app"
        operator: "Equal"
        value: "dev"
        effect: "NoSchedule"
```

### 2. Prod Environment Deployment (`my-deploy-prod.yaml`)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deploy-prod
  labels:
    app: my-deploy-prod
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-deploy-prod
  template:
    metadata:
      labels:
        app: my-deploy-prod
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
      tolerations:
      - key: "app"
        operator: "Equal"
        value: "prod"
        effect: "NoSchedule"
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Taint Worker Nodes**:
   ```bash
   kubectl taint nodes node01 app=dev:NoSchedule
   kubectl taint nodes node02 app=prod:NoSchedule
   ```

2. **Deploy Applications**:
   ```bash
   kubectl apply -f my-deploy-dev.yaml
   kubectl apply -f my-deploy-prod.yaml
   ```

3. **Verify Scheduling**:
   ```bash
   # Confirm dev pods schedule on node01 and prod pods schedule on node02
   kubectl get pods -o wide
   ```

4. **Clean up**:
   ```bash
   kubectl delete -f my-deploy-dev.yaml
   kubectl delete -f my-deploy-prod.yaml
   kubectl taint nodes node01 app=dev:NoSchedule-
   kubectl taint nodes node02 app=prod:NoSchedule-
   ```

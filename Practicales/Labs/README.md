# CKA Practical Labs

## 📌 Overview
This directory contains end-to-end hands-on lab scenarios simulating real CKA exam questions.

---

## 🧪 Lab Scenario 1: Deploying and Exposing Multi-Replica Web Application

### Objective:
1. Create a Deployment named `httpd-frontend` using image `httpd:2.4-alpine`.
2. Configure 3 replicas.
3. Configure the containers to execute:
   `sh -c "echo Hello Kubernetes! && sleep 3600"`
4. Expose the deployment with a NodePort service on port 80.

---

## 📄 Manifest Files in this Directory

### 1. `deployment-definition-1.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd-frontend
  template:
    metadata:
      labels:
        app: httpd-frontend
    spec:
      containers:
      - name: httpd-frontend
        image: httpd:2.4-alpine
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600
```

### 2. `service-definition-1.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: httpd-frontend-service
spec:
  type: NodePort
  selector:
    app: httpd-frontend
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30088
```

---

## 🛠️ Step-by-Step Lab Execution

1. **Deploy the Workload**:
   ```bash
   kubectl apply -f deployment-definition-1.yaml
   kubectl apply -f service-definition-1.yaml
   ```

2. **Verify Deployment & Replicas**:
   ```bash
   kubectl get deployment httpd-frontend
   kubectl get pods -l app=httpd-frontend -o wide
   ```

3. **Verify Service & Endpoints**:
   ```bash
   kubectl get svc httpd-frontend-service
   kubectl get endpoints httpd-frontend-service
   # Confirm endpoints list all 3 pod IPs
   ```

4. **Verify Application Logs**:
   ```bash
   kubectl logs -l app=httpd-frontend --tail=10
   ```

5. **Clean up**:
   ```bash
   kubectl delete -f service-definition-1.yaml
   kubectl delete -f deployment-definition-1.yaml
   ```

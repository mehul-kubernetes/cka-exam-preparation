# 09 - Multiple Schedulers

## 📌 Overview
Kubernetes allows you to run multiple custom schedulers simultaneously alongside the default `kube-scheduler`. When defining a Pod, you can specify which scheduler should schedule that Pod using `spec.schedulerName`.

---

## 📄 Manifest Example: Custom Scheduler Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-custom-scheduler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-custom-scheduler
  template:
    metadata:
      labels:
        app: my-custom-scheduler
    spec:
      serviceAccountName: my-scheduler-sa
      containers:
      - name: scheduler
        image: registry.k8s.io/kube-scheduler:v1.30.0
        command:
        - kube-scheduler
        - --leader-elect=false
        - --scheduler-name=my-custom-scheduler
        - --secure-port=10260
```

---

## 📄 Pod Assigning Custom Scheduler

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: custom-scheduled-pod
spec:
  schedulerName: my-custom-scheduler
  containers:
  - name: nginx
    image: nginx
```

---

## 🛠️ Verification
```bash
# Verify the pod was scheduled by the custom scheduler
kubectl describe pod custom-scheduled-pod
# Look at Events:
# Scheduled: Successfully assigned default/custom-scheduled-pod to node01 by my-custom-scheduler
```

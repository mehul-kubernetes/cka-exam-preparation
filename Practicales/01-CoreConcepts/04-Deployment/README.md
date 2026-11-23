# 04 - Kubernetes Deployments

## 📌 Overview
A **Deployment** provides declarative updates for Pods and ReplicaSets. You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate.

Deployments are a core domain in the **CKA Exam** (25% Application Lifecycle Management).

---

## 🚀 CKA Fast Imperative Commands

Deployments can be completely created and managed via imperative CLI commands:

```bash
# Fast deployment creation
kubectl create deployment my-deploy --image=nginx:1.24 --replicas=3

# Generate deployment YAML without applying
kubectl create deployment my-deploy --image=nginx:1.24 --replicas=3 --dry-run=client -o yaml > deployment.yaml

# Expose deployment via a service
kubectl expose deployment my-deploy --port=80 --target-port=80 --type=ClusterIP

# Scale deployment
kubectl scale deployment my-deploy --replicas=5

# Update image (triggers rolling update)
kubectl set image deployment/my-deploy nginx=nginx:1.25.3 --record

# Check rollout status
kubectl rollout status deployment/my-deploy

# Check rollout history
kubectl rollout history deployment/my-deploy

# Rollback to previous version
kubectl rollout undo deployment/my-deploy

# Rollback to specific revision
kubectl rollout undo deployment/my-deploy --to-revision=2

# Pause and resume rollout
kubectl rollout pause deployment/my-deploy
kubectl rollout resume deployment/my-deploy
```

---

## 📁 Subdirectories
- `01-Deployment/`: Deployment definition and lifecycle management.
- `02-DeploymentStrategy/`: RollingUpdate vs Recreate deployment strategies.

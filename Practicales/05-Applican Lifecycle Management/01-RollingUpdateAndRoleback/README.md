# 01 - Rolling Updates and Rollbacks

## 📌 Overview
Deployments allow you to rollout updates to application code or container images seamlessly. In the CKA exam, you will frequently be asked to update a Deployment's image, verify rollout success, and rollback if the update fails.

---

## 🚀 CKA Imperative Rollout Commands

```bash
# 1. Update deployment image (triggers rolling update)
kubectl set image deployment/frontend-deploy nginx=nginx:1.25.3

# 2. Check rollout status in real-time
kubectl rollout status deployment/frontend-deploy

# 3. View rollout revision history
kubectl rollout history deployment/frontend-deploy

# 4. View details of a specific revision
kubectl rollout history deployment/frontend-deploy --revision=2

# 5. Undo rollout (rollback to immediately previous revision)
kubectl rollout undo deployment/frontend-deploy

# 6. Rollback to a specific historical revision
kubectl rollout undo deployment/frontend-deploy --to-revision=1

# 7. Pause and resume rollout (useful for canary testing)
kubectl rollout pause deployment/frontend-deploy
kubectl rollout resume deployment/frontend-deploy

# 8. Restart a deployment (forces new pod generation without spec changes)
kubectl rollout restart deployment/frontend-deploy
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Create Initial Deployment (v1)**:
   ```bash
   kubectl create deployment web-app --image=nginx:1.24 --replicas=3
   kubectl rollout status deployment/web-app
   ```

2. **Upgrade Deployment (v2)**:
   ```bash
   kubectl set image deployment/web-app nginx=nginx:1.25
   kubectl rollout status deployment/web-app
   ```

3. **Simulate a Broken Image (e.g., ImagePullBackOff)**:
   ```bash
   kubectl set image deployment/web-app nginx=nginx:nonexistent-tag
   kubectl rollout status deployment/web-app
   # Notice deployment pauses because new pods cannot start!
   ```

4. **Rollback to Working Version**:
   ```bash
   kubectl rollout undo deployment/web-app
   kubectl rollout status deployment/web-app
   # Reverts cleanly to nginx:1.25
   ```

5. **Clean up**:
   ```bash
   kubectl delete deployment web-app
   ```

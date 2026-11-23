# 05 - Image Security & Private Registries

## 📌 Overview
Deploying containers from a private registry (e.g., Docker Hub private repos, AWS ECR, Google Artifact Registry) requires credentials. Kubernetes uses `imagePullSecrets` to authenticate with private registries.

---

## 🚀 CKA Fast Imperative Commands

```bash
# Create a docker-registry secret imperatively
kubectl create secret docker-registry my-registry-secret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=myuser \
  --docker-password=mypassword \
  --docker-email=myuser@example.com
```

---

## 📄 Manifest Examples

### 1. Pod with `imagePullSecrets`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: private-pod
spec:
  containers:
  - name: secured-app
    image: myuser/private-repo:v1
  imagePullSecrets:
  - name: my-registry-secret
```

### 2. Linking Secret to ServiceAccount (Automatic Injection)
Instead of specifying `imagePullSecrets` on every Pod, link it to the namespace ServiceAccount:
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: default
imagePullSecrets:
- name: my-registry-secret
```
Or patch via CLI:
```bash
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "my-registry-secret"}]}'
```

---

## 💡 Troubleshooting
If a Pod has `ErrImagePull` or `ImagePullBackOff`:
```bash
kubectl describe pod private-pod
# Check Events section for:
# - Authentication failure / unauthorized
# - Incorrect secret name in imagePullSecrets
# - Wrong registry credentials
```

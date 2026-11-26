# 02 - Argo CD - Declarative GitOps Continuous Delivery

## 📌 Overview
**Argo CD** is a declarative, GitOps continuous delivery tool for Kubernetes. It follows the GitOps pattern of using Git repositories as the single source of truth for defining desired application state in Kubernetes clusters.

Argo CD automates the deployment of the desired application states in the specified target environments. Application deployments can track updates to branches, tags, or pinned to a specific version of manifests at a Git commit.

---

## 🔑 Core Concepts & Architecture

| Concept | Description |
|---|---|
| **GitOps** | Infrastructure and application definitions are stored as declarative code in Git. Deployments are triggered automatically when Git changes. |
| **Desired State vs Live State** | Desired state is what is committed in Git; Live state is what is actually running in the Kubernetes cluster. |
| **Sync Status** | Whether the Live State matches the Desired State (`Synced` vs `OutOfSync`). |
| **Health Status** | Whether the deployed Kubernetes resources are healthy (`Healthy`, `Progressing`, `Degraded`, `Missing`). |
| **Self-Healing** | If a user manually alters or deletes a resource via `kubectl`, Argo CD detects the drift and automatically restores it to match Git. |

---

## 🛠️ Step 1: Installing Argo CD in Kubernetes

```bash
# 1. Create dedicated namespace
kubectl create namespace argocd

# 2. Apply official Argo CD manifests
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 3. Wait for all Argo CD pods to become Ready
kubectl wait --namespace argocd \
  --for=condition=ready pod \
  --all \
  --timeout=300s
```

---

## 🌐 Step 2: Accessing the Argo CD Web UI & CLI

### 1. Port-Forward to the Server
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Open your browser and navigate to: **`https://localhost:8080`**

### 2. Retrieve Initial Admin Password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
echo ""
```
- **Username**: `admin`
- **Password**: *(The decoded string from above)*

### 3. Log In via Argo CD CLI
```bash
argocd login localhost:8080 --username admin --password <password> --insecure
```

---

## 📄 Manifest Example: Argo CD `Application` CRD

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook-app
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true      # Automatically delete resources if removed from Git
      selfHeal: true   # Automatically revert unauthorized manual kubectl changes
    syncOptions:
      - CreateNamespace=true
```

---

## ⚙️ Essential Argo CD CLI Commands

```bash
# 1. List all managed applications
argocd app list

# 2. Get detailed application status
argocd app get guestbook-app

# 3. Manually trigger a sync
argocd app sync guestbook-app

# 4. View sync history
argocd app history guestbook-app

# 5. Rollback to a specific revision
argocd app rollback guestbook-app <revision-id>

# 6. Delete application
argocd app delete guestbook-app
```

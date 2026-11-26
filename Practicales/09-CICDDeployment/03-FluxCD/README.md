# 03 - Flux CD - The GitOps Toolkit

## 📌 Overview
**Flux CD** is a set of continuous and progressive delivery solutions for Kubernetes that are open and extensible. It is a CNCF Graduated project that provides native GitOps operators for Kubernetes.

Unlike Argo CD (which offers a centralized UI and API server), Flux is architected as a set of specialized, composable Kubernetes controllers called the **GitOps Toolkit**.

---

## 🔑 Core Controllers & Custom Resources

| Controller | Custom Resource Definition (CRD) | Role |
|---|---|---|
| **Source Controller** | `GitRepository`, `OCIRepository`, `HelmRepository` | Fetches, validates, and stores artifacts from Git, OCI registries, or Helm repos in cluster memory. |
| **Kustomize Controller** | `Kustomization` | Applies Kustomize overlays and reconciles cluster state against Git sources. |
| **Helm Controller** | `HelmRelease` | Declaratively manages Helm chart releases and lifecycles. |
| **Notification Controller** | `Alert`, `Provider`, `Receiver` | Dispatches webhooks (Slack, MS Teams, GitHub commit status). |

---

## 🛠️ Step 1: Installing the Flux CLI

### Linux / macOS
```bash
curl -s https://fluxcd.io/install.sh | sudo bash
```

### Windows (PowerShell)
```powershell
choco install flux
# or
winget install FluxCD.Flux
```

### Verify Pre-requisites
```bash
flux check --pre
```

---

## 🚀 Step 2: Bootstrapping Flux to a Git Repository

Bootstrapping installs Flux on your cluster and configures it to synchronize with your GitHub repository:

```bash
export GITHUB_TOKEN="<your-personal-access-token>"

flux bootstrap github \
  --owner=<github-username-or-org> \
  --repository=<repo-name> \
  --branch=main \
  --path=clusters/my-cluster \
  --personal
```

---

## 📄 Manifest Examples

### 1. Define Git Source (`GitRepository`)
```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: podinfo
  namespace: flux-system
spec:
  interval: 1m
  url: https://github.com/stefanprodan/podinfo
  ref:
    branch: master
```

### 2. Define Kustomize Delivery (`Kustomization`)
```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: podinfo-dev
  namespace: flux-system
spec:
  interval: 5m
  targetNamespace: default
  sourceRef:
    kind: GitRepository
    name: podinfo
  path: "./kustomize"
  prune: true
  wait: true
```

---

## ⚙️ Essential Flux CLI Commands

```bash
# 1. View status of Git repositories
flux get sources git

# 2. View status of Kustomization deployments
flux get kustomizations

# 3. View status of Helm releases
flux get helmreleases

# 4. Trigger an immediate Git source check
flux reconcile source git podinfo

# 5. Trigger an immediate Kustomize reconciliation
flux reconcile kustomization podinfo-dev --with-source

# 6. Suspend and resume auto-sync (useful for maintenance)
flux suspend kustomization podinfo-dev
flux resume kustomization podinfo-dev
```

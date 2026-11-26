# 01 - Kustomize - Declarative Configuration Management

## 📌 Overview
**Kustomize** is a standalone tool and a native component of `kubectl` that introduces a template-free way to customize Kubernetes application manifests without modifying the original YAML files.

Kustomize uses the **Base and Overlays** pattern to maintain a single source of truth while applying environment-specific variations (such as replicas, image tags, namespaces, and resource limits) for `dev`, `staging`, and `production`.

---

## 🔑 Key Features & Why Kustomize?
1. **Template-free**: Uses pure Kubernetes YAML without proprietary template engines or variable substitution brackets (`{{ .Values }}`).
2. **Built directly into `kubectl`**: Run `kubectl apply -k <directory>` without installing third-party binaries.
3. **Deterministic & Inspectable**: Run `kubectl kustomize <directory>` to see the exact generated YAML output before applying.
4. **GitOps Friendly**: Native integration with Argo CD and Flux.

---

## 🏗️ Typical Base & Overlays Directory Layout

```text
app-kustomize/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/
    │   ├── kustomization.yaml
    │   └── replica-patch.yaml
    └── prod/
        ├── kustomization.yaml
        └── resource-limits-patch.yaml
```

---

## 📄 Manifest Examples

### 1. The Base: `base/kustomization.yaml`
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml

commonLabels:
  app: my-webapp
```

### 2. The Dev Overlay: `overlays/dev/kustomization.yaml`
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - ../../base

namePrefix: dev-
namespace: development

images:
  - name: nginx
    newTag: "1.25.3-alpine"

patches:
  - target:
      kind: Deployment
      name: my-webapp
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 1
```

### 3. The Prod Overlay: `overlays/prod/kustomization.yaml`
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - ../../base

namePrefix: prod-
namespace: production

configMapGenerator:
  - name: app-config
    literals:
      - DB_HOST=prod-db.internal
      - LOG_LEVEL=error

patches:
  - target:
      kind: Deployment
      name: my-webapp
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 5
```

---

## 🛠️ Essential CLI Commands

```bash
# 1. Preview generated manifests without applying (stdout)
kubectl kustomize overlays/dev/
kubectl kustomize overlays/prod/

# 2. Apply directly to cluster using kubectl -k
kubectl apply -k overlays/dev/
kubectl apply -k overlays/prod/

# 3. Delete resources managed by kustomization
kubectl delete -k overlays/dev/
```

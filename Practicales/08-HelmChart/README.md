# 08 - Helm - The Package Manager for Kubernetes

## 📌 Overview
**Helm** is the official package manager for Kubernetes. Helm manages Kubernetes applications through **Charts**, which are packages of pre-configured Kubernetes resources.

Helm helps you:
1. Define, install, and upgrade even the most complex Kubernetes applications.
2. Manage configuration variations using dynamic Go templates and `values.yaml`.
3. Track release versions, inspect deployment history, and perform instantaneous rollbacks.

---

## 🔑 Core Helm Concepts

| Term | Description |
|---|---|
| **Chart** | A package of Kubernetes YAML templates and configuration files. |
| **Repository** | A collection of published charts (e.g. Artifact Hub, Bitnami, ChartMuseum). |
| **Release** | A specific, running instance of a Chart deployed into a Kubernetes cluster. |
| **Values** | Configuration variables (`values.yaml` or `--set`) passed into templates during install/upgrade. |

---

## 📁 Standard Helm Chart Directory Structure

```text
my-chart/
├── Chart.yaml          # Metadata: name, version, description, appVersion
├── values.yaml         # Default configuration values
├── charts/             # Dependency charts
├── templates/          # Kubernetes manifest templates
│   ├── deployment.yaml # Deployment template using {{ .Values... }}
│   ├── service.yaml    # Service template
│   ├── hpa.yaml        # HorizontalPodAutoscaler template
│   ├── _helpers.tpl    # Reusable template snippets / partials
│   └── NOTES.txt       # User guide displayed after helm install
└── .helmignore         # Files to ignore when packaging
```

---

## 🚀 Complete Helm CLI Workflow

### 1. Creating and Validating a Chart
```bash
# Create a chart scaffolding template
helm create my-chart

# Lint chart for syntax and formatting errors
helm lint ./my-chart

# Render templates locally without contacting the cluster (Dry-run / debugging):
helm template my-release ./my-chart
helm template my-release ./my-chart -f custom-values.yaml
```

### 2. Installing Charts
```bash
# Install from local directory
helm install my-webapp ./my-chart -n default

# Install overriding specific values via CLI
helm install my-webapp ./my-chart --set replicaCount=3 --set image.tag=1.25.3

# Install overriding values using a custom values file
helm install my-webapp ./my-chart -f values-prod.yaml
```

### 3. Working with Remote Repositories
```bash
# Add a chart repository (e.g. Bitnami)
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Search for charts
helm search repo nginx
helm search hub redis

# Install directly from remote repository
helm install my-redis bitnami/redis --set auth.enabled=false
```

### 4. Upgrading, History & Rollback
```bash
# List installed releases
helm list -A

# Upgrade release with new values or chart version
helm upgrade my-webapp ./my-chart --set replicaCount=5

# Check release revision history
helm history my-webapp
# Shows: REVISION, UPDATED, STATUS, CHART, DESCRIPTION

# Rollback to revision 1 instantaneously:
helm rollback my-webapp 1

# Check status of release resources
helm status my-webapp
```

### 5. Uninstalling Releases
```bash
helm uninstall my-webapp -n default
```

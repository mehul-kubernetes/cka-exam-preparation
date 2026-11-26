# 09 - Continuous Integration & Continuous Delivery (CI/CD)

## 📌 Overview
This directory covers modern declarative deployment workflows, configuration customization, and GitOps continuous delivery mechanisms for Kubernetes.

As organizations scale their Kubernetes footprints, managing raw YAML files across different environments (Development, Staging, Production) becomes impractical. Modern workflows rely on **Kustomize** and **GitOps engines (Argo CD & Flux CD)** to automate and synchronize cluster state directly from Git repositories.

---

## 📂 Subtopics in this Module

| Directory | Topic | Key Focus |
|---|---|---|
| [`01-Kustomize`](./01-Kustomize/README.md) | **Kustomize Configuration Management** | Template-free declarative customization, Base and Overlays pattern, `kustomization.yaml`, `kubectl apply -k`. |
| [`02-ArgoCD`](./02-ArgoCD/README.md) | **Argo CD GitOps Continuous Delivery** | Declarative GitOps engine, `Application` CRD, automated sync, self-healing, Web UI & CLI management. |
| [`03-FluxCD`](./03-FluxCD/README.md) | **Flux CD GitOps Toolkit** | CNCF graduated GitOps operator, `GitRepository`, `Kustomization`, `HelmRelease` controllers. |

---

## 🔄 The Modern Kubernetes GitOps Workflow
```
+--------------------------------------------------------------+
|                       Git Repository                         |
|   (Source of Truth: Kustomize Base & Overlays / Helm)        |
+------------------------------+-------------------------------+
                               |
                               | (Polls / Webhook)
                               v
+--------------------------------------------------------------+
|                   GitOps Engine (Argo / Flux)                |
|             (Runs inside the Kubernetes Cluster)             |
|                                                              |
|   - Compares Desired State (Git) vs Live State (K8s Cluster) |
|   - Automatically triggers Sync / Self-Healing               |
+------------------------------+-------------------------------+
                               |
                               v (Reconciles)
+--------------------------------------------------------------+
|                 Kubernetes Cluster Workloads                 |
|            (Deployments, Services, ConfigMaps, etc.)         |
+--------------------------------------------------------------+
```

# ☸️ Certified Kubernetes Administrator (CKA) Complete Preparation Guide

Welcome to the **Certified Kubernetes Administrator (CKA)** Exam Preparation repository! This workspace is an exhaustive, production-grade learning suite featuring practical labs, step-by-step hands-on guides, YAML manifest templates, and exam questions with verified solutions.

---

## 📚 Repository Structure

```
cka-exam-preparation/
├── Documents/                     # Comprehensive theory guides, question banks, and solutions (PDF & DOCX)
├── Practicales/                   # Step-by-step CKA practical exercises categorized by domain
│   ├── 01-CoreConcepts/          # Pods, Multi-Container Pods, RC, ReplicaSets, Deployments, Services, Namespaces
│   ├── 02-Scheduling/            # Labels, Selectors, Taints/Tolerations, NodeAffinity, Limits, StaticPods, DaemonSets
│   ├── 03-LoggingAndMonitoring/  # Pod logs, previous crashed logs, journalctl, metrics-server, top command
│   ├── 04-Storage/               # Volumes (emptyDir, hostPath), PVs, PVCs, StorageClasses, dynamic provisioning
│   ├── 05-Applican Lifecycle.../ # Rolling updates, rollbacks, deployment strategies, env vars, Downward API, ConfigMaps, Secrets, Sidecars, InitContainers, HealthChecks (Probes)
│   ├── 06-ClusterManagement/     # Installation (Kubeadm, Minikube, Kind, K3d), ETCD backup/restore, version upgrades
│   ├── 07-Security/              # CSRs, Kubeconfig, Roles, RoleBindings, ClusterRoles, SAs, imagePullSecrets, NetworkPolicies
│   ├── 08-HelmChart/             # Helm package manager, chart templating, releases, upgrades/rollbacks
│   ├── 09-CICDDeployment/        # Kustomize (base/overlays), Argo CD, and Flux CD GitOps continuous delivery
│   └── Labs/                     # Multi-resource exam scenarios and simulation exercises
└── Scenarios/                     # Advanced real-world infrastructure and networking setups
    ├── CNI/                      # Weave Net DaemonSet overlay network
    └── Ingress/                  # NGINX Ingress Controller setups (Kind, Cloud, Baremetal) and routing rules
```

---

## 🧭 CKA Exam Curriculum Weightage

| Domain | Weight | Core Topics |
|---|:---:|---|
| **Cluster Architecture, Installation & Configuration** | **25%** | Kubeadm bootstrap, ETCD backup & restore, version upgrades, RBAC, Kubeconfig |
| **Workloads & Scheduling** | **15%** | Deployments, rolling updates, multi-container pods, taints & tolerations, node affinity, resource limits, DaemonSets |
| **Services & Networking** | **20%** | ClusterIP, NodePort, LoadBalancer, Ingress controllers, CoreDNS troubleshooting, NetworkPolicies |
| **Storage** | **10%** | Volumes, PersistentVolumes, PersistentVolumeClaims, StorageClasses, dynamic provisioning |
| **Troubleshooting** | **30%** | Application failures, worker node failures, cluster component failures, network routing |

---

## ⚡ CKA Exam Speed Cheat Sheet

Run this at the start of your 2-hour exam session:

```bash
# 1. Alias kubectl and enable auto-completion
alias k=kubectl
complete -o default -F __start_kubectl k

# 2. Fast imperative dry-run helper
export do="--dry-run=client -o yaml"

# 3. Fast force-delete helper (instant termination)
export now="--force --grace-period=0"

# 4. Configure vim for YAML indentation (2 spaces, no tabs)
cat <<EOF > ~/.vimrc
set tabstop=2
set shiftwidth=2
set expandtab
set number
EOF
```

---

## 🚀 Navigation Links

- 📖 **[Practicals Master Index](./Practicales/README.md)**
- 📘 **[Study Documents & Question Solutions](./Documents/README.md)**
- 🌐 **[Real-World Ingress & CNI Scenarios](./Scenarios/README.md)**

# ☸️ Certified Kubernetes Administrator (CKA) Practicals Guide

Welcome to the **CKA Exam Preparation Practicals** repository! This collection is meticulously designed to provide hands-on practical exercises, production-grade manifest templates, and high-speed CLI workflows tailored to help you pass the CNCF **Certified Kubernetes Administrator (CKA)** exam.

---

## 🧭 CKA Exam Curriculum Weightage

| Domain | Weight | Core Topics Covered |
|---|:---:|---|
| **Cluster Architecture, Installation & Configuration** | **25%** | Kubeadm cluster installation, ETCD backup & restore, version upgrades, RBAC, Kubeconfig |
| **Workloads & Scheduling** | **15%** | Deployments, rolling updates, multi-container pods, taints & tolerations, node affinity, resource limits |
| **Services & Networking** | **20%** | ClusterIP, NodePort, LoadBalancer, Ingress controllers, CoreDNS troubleshooting, NetworkPolicies |
| **Storage** | **10%** | Volumes, PersistentVolumes, PersistentVolumeClaims, StorageClasses, dynamic provisioning |
| **Troubleshooting** | **30%** | Application failures, worker node failures, cluster component failures, network routing |

---

## 📂 Practicals Index & Directory Navigation

Click any practical module below to navigate directly to its documentation and hands-on exercises:

| # | Topic | Description & Links |
|:---:|---|---|
| **01** | **[Pod](./01-Pod/README.md)** | Single container and multi-container Pod lifecycle, pod specs, port mappings. <br>↳ *Sub-topic:* **[Multi-Container Pods](./01-Pod/02-MultiContainerPod/README.md)** |
| **02** | **[ReplicationController](./02-ReplicationController/README.md)** | Legacy pod replication controller, equality selectors, self-healing. |
| **03** | **[ReplicaSet](./03-Replicaset/README.md)** | Set-based selectors (`matchExpressions`), scaling, and controller reconciliation. |
| **04** | **[Deployment](./04-Deployment/README.md)** | Declarative updates, rolling rollouts, undo/history, and pause/resume. <br>↳ *Sub-topic:* **[01-Deployment](./04-Deployment/01-Deployment/README.md)** & **[02-DeploymentStrategy](./04-Deployment/02-DeploymentStrategy/README.md)** |
| **05** | **[Service](./05-Service/README.md)** | Service types: ClusterIP, NodePort, and LoadBalancer; Endpoints debugging and DNS. |
| **06** | **[Namespace](./06-Namespace/README.md)** | Multi-tenancy, cross-namespace DNS FQDN, and context namespace switching. |
| **07** | **[Labels](./07-Labels/README.md)** | Object metadata tagging, imperative label manipulation, and node labeling. |
| **08** | **[Selector](./08-Selector/README.md)** | Equality vs set-based filtering, `-l` query expressions, service label selectors. |
| **09** | **[Taints & Tolerations](./09-TaintsAndTolerations/README.md)** | Node repelling, taint effects (`NoSchedule`, `NoExecute`), toleration syntax. <br>↳ *Sub-topics:* **[deployment](./09-TaintsAndTolerations/deployment/README.md)** & **[pod](./09-TaintsAndTolerations/pod/README.md)** |
| **10** | **[NodeAffinity](./10-NodeAffinity/README.md)** | Advanced node scheduling: `required` (hard) vs `preferred` (soft) affinity. |
| **11** | **[Volume](./11-Volume/README.md)** | Pod storage abstractions: `emptyDir`, `hostPath`, shared pod volumes. |
| **12** | **[PersistentVolume (PV)](./12-Persistanvolume/README.md)** | Cluster-scoped storage assets, access modes, and reclaim policies (`Retain`, `Delete`). |
| **13** | **[PersistentVolumeClaim (PVC)](./13-PersistantVolumeClaim/README.md)** | Requesting persistent storage, volume binding, and pod mounting. |
| **14** | **[StorageClass](./14-StorageClass/README.md)** | Dynamic provisioning, provisioners, and `WaitForFirstConsumer` binding mode. |
| **15** | **[SidecarContainer](./15-SidecarContainer/README.md)** | Log streaming sidecars, shared volumes, and native sidecars (K8s 1.28+). |
| **16** | **[InitContainer](./16-InitContainer/README.md)** | Sequential initialization, service readiness checks, initialization lifecycle. |
| **17** | **[ConfigMap](./17-ConfigMap/README.md)** | Environment configuration injection, `envFrom`, single keys, and volume mounts. |
| **18** | **[Secrets](./18-Secrets/README.md)** | Base64-encoded confidential configuration, TLS secrets, and registry credentials. |
| **19** | **[ClusterManagement](./19-ClusterManagement/README.md)** | End-to-end cluster lifecycle and operations: <br>↳ **[01-Installation](./19-ClusterManagement/01-Installation/README.md)**: Kubeadm cluster bootstrap <br>↳ **[02-ETCDBackup](./19-ClusterManagement/02-ETCDBackup/README.md)**: ETCD snapshot save and restore <br>↳ **[03-VersionUpgrade](./19-ClusterManagement/03-VersionUpgrade/README.md)**: Kubeadm node upgrade workflows |
| **20** | **[Security & RBAC](./20-Security/README.md)** | Authentication, authorization, and isolation: <br>↳ **[01-AuthenticationAndAuthorisation](./20-Security/01-AuthenticationAndAuthorisation/README.md)**: CSRs, certs, `kubectl auth can-i` <br>↳ **[02-KubeConfig](./20-Security/02-KubeConfig/README.md)**: Contexts, clusters, users, switching <br>↳ **[03-RoleBinding](./20-Security/03-RoleBinding/README.md)**: Roles & RoleBindings <br>&nbsp;&nbsp;&nbsp;&nbsp;↳ **[01-ClusterRole](./20-Security/03-RoleBinding/01-ClusterRole/README.md)** <br>&nbsp;&nbsp;&nbsp;&nbsp;↳ **[02-ClusterRoleBinding](./20-Security/03-RoleBinding/02-ClusterRoleBinding/README.md)** <br>↳ **[04-ServiceAccount](./20-Security/04-ServiceAccount/README.md)**: Workload identities <br>↳ **[05-ImageSecurity](./20-Security/05-ImageSecurity/README.md)**: Private image registries & pull secrets <br>↳ **[06-NetworkPolicy](./20-Security/06-NetworkPolicy/README.md)**: Ingress/egress network firewalls |
| **Labs**| **[CKA Practical Labs](./Labs/README.md)** | Hands-on multi-resource scenario exercises simulating CKA exam tasks. |

---

## ⚡ Essential CKA Exam Speed Setup

At the very beginning of your exam, execute these standard commands to optimize speed:

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

## 🎯 Best Practices for the CKA Exam
1. **Never write YAML from scratch**: Use `k run`, `k create deployment`, `k expose`, or `k create <resource>` with `$do` to generate clean YAML templates.
2. **Context checking**: Each question gives you a specific context switch command (e.g. `kubectl config use-context k8s`). **Run it first** before solving!
3. **Verify every step**: Always confirm your pods are running and services are bound with `kubectl get <resource> -n <ns>` before moving to the next question.

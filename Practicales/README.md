# ☸️ Certified Kubernetes Administrator (CKA) Practicals Guide

Welcome to the **CKA Exam Preparation Practicals** directory! This collection is organized according to the official CNCF Certified Kubernetes Administrator curriculum domains.

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

## 📂 Practicals Domain Navigation

Click any domain or practical module below to jump directly to its documentation:

### 1. [01-CoreConcepts](./01-CoreConcepts/README.md)
Foundational Kubernetes workload units and cluster isolation:
- 📄 **[01-Pod](./01-CoreConcepts/01-Pod/README.md)**: Pod lifecycle, single-container specs, port mappings.
  - ↳ **[01-SingleContainer](./01-CoreConcepts/01-Pod/01-SingleContainer/README.md)**: Entrypoint/CMD overrides, exit codes, CrashLoopBackOff, live logs.
  - ↳ **[02-MultiContainerPod](./01-CoreConcepts/01-Pod/02-MultiContainerPod/README.md)**: Shared network (`localhost`), shared volumes, sidecar patterns.
  - ↳ **[03-Veriables](./01-CoreConcepts/01-Pod/03-Veriables/README.md)**: Plain env vars, ConfigMap/Secret refs, Downward API.
  - ↳ **[04-DefaultPodStorage](./01-CoreConcepts/01-Pod/04-DefaultPodStorage/README.md)**: Ephemeral writable layer, data loss on restart, volume persistence comparison.
- 📄 **[02-ReplicationController](./01-CoreConcepts/02-ReplicationController/README.md)**: Legacy replica controller, equality selectors.
- 📄 **[03-Replicaset](./01-CoreConcepts/03-Replicaset/README.md)**: Set-based selectors (`matchExpressions`), replica scaling.
- 📄 **[04-Deployment](./01-CoreConcepts/04-Deployment/README.md)**: Declarative updates, rollout history, and pause/resume.
  - ↳ **[01-Deployment](./01-CoreConcepts/04-Deployment/01-Deployment/README.md)**: Deployment manifest specifications.
  - ↳ **[02-DeploymentStrategy](./01-CoreConcepts/04-Deployment/02-DeploymentStrategy/README.md)**: `RollingUpdate` vs `Recreate`.
- 📄 **[05-Service](./01-CoreConcepts/05-Service/README.md)**: Microservice networking (`ClusterIP`, `NodePort`, `LoadBalancer`, Endpoints).
- 📄 **[05-StatefulSet](./01-CoreConcepts/05-StatefulSet/README.md)**: Persistent identity, headless services, ordered scaling, `volumeClaimTemplates`.
- 📄 **[06-Namespace](./01-CoreConcepts/06-Namespace/README.md)**: Logical isolation, cross-namespace DNS FQDN, context namespace switching.

---

### 2. [02-Scheduling](./02-Scheduling/README.md)
Advanced node selection and pod placement policies:
- 🏷️ **[01-Labels](./02-Scheduling/01-Labels/README.md)**: Object metadata tagging, node labeling.
- 🔍 **[02-Selector](./02-Scheduling/02-Selector/README.md)**: Equality and set-based filtering expressions (`-l`).
- 🚫 **[03-TaintsAndTolerations](./02-Scheduling/03-TaintsAndTolerations/README.md)**: Node repelling, `NoSchedule`, `NoExecute`, pod toleration specs.
  - ↳ **[deployment](./02-Scheduling/03-TaintsAndTolerations/deployment/README.md)**
  - ↳ **[pod](./02-Scheduling/03-TaintsAndTolerations/pod/README.md)**
- 👥 **[04-PodAffinity](./02-Scheduling/04-PodAffinity/README.md)**: Inter-pod affinity (co-location) and anti-affinity (high availability).
- 🧲 **[05-NodeAffinity](./02-Scheduling/05-NodeAffinity/README.md)**: Hard (`required...`) and soft (`preferred...`) affinity placement rules.
- ⚖️ **[06-ResourceLimit](./02-Scheduling/06-ResourceLimit/README.md)**: CPU/memory requests and limits, OOMKilled (Exit Code 137).
- ⚙️ **[07-StaticPod](./02-Scheduling/07-StaticPod/README.md)**: Kubelet-managed control-plane pods (`/etc/kubernetes/manifests`).
- 🤖 **[08-DaemonSet](./02-Scheduling/08-DaemonSet/README.md)**: Node-level agent pods, DaemonSet manifest techniques.
- 🔀 **[09-MultipleSchedulers](./02-Scheduling/09-MultipleSchedulers/README.md)**: Custom scheduler deployment and `spec.schedulerName`.

---

### 3. [03-LoggingAndMonitoring](./03-LoggingAndMonitoring/README.md)
Application and cluster observability:
- 📜 **Container Logs**: `kubectl logs`, live streaming (`-f`), multi-container inspection (`-c`).
- 💥 **Crashed Containers**: Inspecting previous logs (`kubectl logs -p / --previous`).
- 🖥️ **Systemd Services**: Kubelet and containerd journal logs (`journalctl -u kubelet`).
- 📊 **Resource Metrics**: Node and Pod resource consumption via `kubectl top`.

---

### 4. [04-Storage](./04-Storage/README.md)
Ephemeral and persistent storage in Kubernetes:
- 💾 **[01-Volume](./04-Storage/01-Volume/README.md)**: `emptyDir` (shared cache), `hostPath` (node filesystem).
- 📦 **[02-Persistanvolume](./04-Storage/02-Persistanvolume/README.md)**: Cluster-scoped PersistentVolumes, access modes, reclaim policies (`Retain`, `Delete`).
- 📑 **[04-PersistantVolumeClaim](./04-Storage/04-PersistantVolumeClaim/README.md)**: Requesting storage, volume binding, pod PVC mounts.
- ⚡ **[05-StorageClass](./04-Storage/05-StorageClass/ReadMe.md)**: Dynamic volume provisioning, provisioners, `WaitForFirstConsumer`.

---

### 5. [05-Applican Lifecycle Management](./05-Applican%20Lifecycle%20Management/README.md)
Managing rollouts, strategies, configuration, and patterns:
- 🔄 **[01-RollingUpdateAndRoleback](./05-Applican%20Lifecycle%20Management/01-RollingUpdateAndRoleback/README.md)**: Image updates, rollout status, revision history, rollbacks.
- 📐 **[02-DeploymentStretegy](./05-Applican%20Lifecycle%20Management/02-DeploymentStretegy/README.md)**: Tuning `maxSurge` & `maxUnavailable`, zero-downtime rollouts.
- 🧩 **[03-ConfigureVeriables](./05-Applican%20Lifecycle%20Management/03-ConfigureVeriables/README.md)**: Plain env vars, Downward API (`fieldRef`, `resourceFieldRef`).
- 📋 **[04-ConfigMap](./05-Applican%20Lifecycle%20Management/04-ConfigMap/README.md)**: Injecting non-confidential configuration as env vars or volumes.
- 🔐 **[05-Secrets](./05-Applican%20Lifecycle%20Management/05-Secrets/README.md)**: Base64 sensitive data, TLS secrets, Docker registry credentials.
- 🚚 **[06-SidecarContainer](./05-Applican%20Lifecycle%20Management/06-SidecarContainer/README.md)**: Log streaming sidecars, shared emptyDir, native sidecars (K8s 1.28+).
- ⏳ **[07-InitContainer](./05-Applican%20Lifecycle%20Management/07-InitContainer/README.md)**: Pre-initialization routines, blocking dependency readiness checks.
- 🩺 **[08-HealthCheck](./05-Applican%20Lifecycle%20Management/08-HealthCheck/README.md)**: Container Probes & Health Checks:
  - ↳ 💓 **[01-LivenessProbe](./05-Applican%20Lifecycle%20Management/08-HealthCheck/01-LivenessProbe/README.md)**: Deadlock detection & automatic container restarts
  - ↳ 🚦 **[02-ReadinessProbe](./05-Applican%20Lifecycle%20Management/08-HealthCheck/02-ReadinessProbe/README.md)**: Service endpoints gating & traffic routing
  - ↳ 🚀 **[03-StartupProbe](./05-Applican%20Lifecycle%20Management/08-HealthCheck/03-StartupProbe/README.md)**: Protecting slow-starting applications

---

### 6. [06-ClusterManagement](./06-ClusterManagement/README.md)
Cluster lifecycle, bootstrap, and backup maintenance:
- 🛠️ **[01-Installation](./06-ClusterManagement/01-Installation/README.md)**: Cluster setup with `kubeadm`, plus local environments:
  - ↳ 📦 **[01-Minikube](./06-ClusterManagement/01-Installation/01-Minikube/README.md)**: Local single/multi-node clusters, addons, tunneling
  - ↳ 🐳 **[02-KindCluster](./06-ClusterManagement/01-Installation/02-KindCluster/README.md)**: Multi-node clusters in Docker, containerd, port mappings
  - ↳ ⚡ **[03-K3dCluster](./06-ClusterManagement/01-Installation/03-K3dCluster/README.md)**: Ultra-lightweight k3s in Docker (~512MB RAM)
- 💾 **[02-ETCDBackup](./06-ClusterManagement/02-ETCDBackup/README.md)**: `etcdctl snapshot save`, TLS certificate paths, restore to new data directory.
- 🚀 **[03-VersionUpgrade](./06-ClusterManagement/03-VersionUpgrade/README.md)**: Kubeadm sequential minor version upgrades, node draining, uncordoning.

---

### 7. [07-Security](./07-Security/README.md)
Authentication, authorization, identity, and isolation:
- 🛡️ **[01-AuthenticationAndAuthorisation](./07-Security/01-AuthenticationAndAuthorisation/README.md)**: CSR generation, signing workflow, `kubectl auth can-i`.
- 🔑 **[02-KubeConfig](./07-Security/02-KubeConfig/README.md)**: Clusters, users, contexts, `kubectl config use-context`.
- 📜 **[03-RoleBinding](./07-Security/03-RoleBinding/README.md)**: Namespaced Roles and RoleBindings.
  - ↳ **[01-ClusterRole](./07-Security/03-RoleBinding/01-ClusterRole/README.md)**: Cluster-scoped roles, namespace role reuse.
  - ↳ **[02-ClusterRoleBinding](./07-Security/03-RoleBinding/02-ClusterRoleBinding/README.md)**: Global cluster permissions binding.
- 👤 **[04-ServiceAccount](./07-Security/04-ServiceAccount/README.md)**: In-cluster process identities, token generation, pod attachment.
- 🐳 **[05-ImageSecurity](./07-Security/05-ImageSecurity/README.md)**: Private image registries and `imagePullSecrets`.
- 🧱 **[06-NetworkPolicy](./07-Security/06-NetworkPolicy/README.md)**: Default deny-all ingress, namespace selectors, port-level firewalls.

---

### 8. [08-HelmChart](./08-HelmChart/README.md)
The Kubernetes package manager:
- ⛵ **[Helm](./08-HelmChart/README.md)**: Package management, templating, `values.yaml`, upgrades, rollbacks, and repositories.

---

### 9. [09-CICDDeployment](./09-CICDDeployment/README.md)
Declarative GitOps and modern CI/CD deployment workflows:
- 🛠️ **[01-Kustomize](./09-CICDDeployment/01-Kustomize/README.md)**: Template-free configuration management (`base` & `overlays`).
- 🐙 **[02-ArgoCD](./09-CICDDeployment/02-ArgoCD/README.md)**: Declarative GitOps continuous delivery and self-healing.
- ⚡ **[03-FluxCD](./09-CICDDeployment/03-FluxCD/README.md)**: CNCF graduated GitOps toolkit, source controllers, and automated reconciliation.

---

### 10. [Labs](./Labs/README.md)
- 🧪 **[CKA Practical Labs](./Labs/README.md)**: End-to-end multi-resource scenario exercises simulating CKA exam tasks.

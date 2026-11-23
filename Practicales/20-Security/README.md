# 20 - Kubernetes Security & RBAC

## 📌 Overview
Security and RBAC (Role-Based Access Control) make up **15% (Services & Networking, Troubleshooting, and Security)** of the CKA exam.

Security encompasses:
1. **Authentication (AuthN)**: Who are you? (Certificates, OpenID Connect, ServiceAccounts).
2. **Authorization (AuthZ)**: What are you allowed to do? (RBAC: Roles, RoleBindings, ClusterRoles, ClusterRoleBindings).
3. **KubeConfig**: Managing multiple clusters, users, and contexts.
4. **ServiceAccounts**: Identity for in-cluster processes and Pods.
5. **Image Security**: Pulling from private registries with `imagePullSecrets`.
6. **NetworkPolicies**: Restricting traffic ingress/egress between Pods.

---

## 📁 Subtopics in this Directory

| Subdirectory | Topic | Key Focus |
|---|---|---|
| [`01-AuthenticationAndAuthorisation`](./01-AuthenticationAndAuthorisation/README.md) | AuthN & AuthZ Concepts | X.509 certs, `CertificateSigningRequest` (CSR), RBAC matrix. |
| [`02-KubeConfig`](./02-KubeConfig/README.md) | Kubeconfig Files | `clusters`, `users`, `contexts`, `kubectl config` management. |
| [`03-RoleBinding`](./03-RoleBinding/README.md) | RBAC Roles & Bindings | Namespaced Roles vs Cluster-wide ClusterRoles. |
| [`04-ServiceAccount`](./04-ServiceAccount/README.md) | ServiceAccounts | Creating SAs, assigning to Pods, projected tokens. |
| [`05-ImageSecurity`](./05-ImageSecurity/README.md) | Image Security | `docker-registry` secret, `imagePullSecrets`. |
| [`06-NetworkPolicy`](./06-NetworkPolicy/README.md) | Network Policies | Ingress & Egress firewalls, label selectors, port restrictions. |

# 02 - KubeConfig Management

## 📌 Overview
A **kubeconfig** file is used to configure access to one or more Kubernetes clusters. It consists of three primary sections:
- **`clusters`**: Endpoint URLs and CA certificates of clusters.
- **`users`**: Credentials (certificates, tokens, passwords) to authenticate against the cluster.
- **`contexts`**: Triplet pairing a `cluster`, a `user`, and optionally a default `namespace`.

Default location: `~/.kube/config` (or referenced by the `KUBECONFIG` environment variable).

---

## 🚀 CKA Fast Imperative Commands

```bash
# View kubeconfig
kubectl config view
kubectl config view --minify   # Displays ONLY the current active context

# Get list of contexts
kubectl config get-contexts

# Get currently active context name
kubectl config current-context

# Switch to a different context
kubectl config use-context production-cluster

# Set a default namespace for the current context
kubectl config set-context --current --namespace=finance

# Create a new context
kubectl config set-context dev-context \
  --cluster=kubernetes \
  --user=developer \
  --namespace=dev

# Merge multiple kubeconfig files
export KUBECONFIG=~/.kube/config:/path/to/custom-kubeconfig
kubectl config view --flatten > ~/.kube/merged-config
```

---

## 📄 Manifest Structure

```yaml
apiVersion: v1
kind: Config
preferences: {}

clusters:
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://192.168.1.100:6443
  name: k8s-cluster

users:
- name: dev-user
  user:
    client-certificate: /path/to/dev-user.crt
    client-key: /path/to/dev-user.key

contexts:
- context:
    cluster: k8s-cluster
    namespace: development
    user: dev-user
  name: dev-context

current-context: dev-context
```

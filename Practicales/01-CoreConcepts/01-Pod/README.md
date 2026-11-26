# 01 - Pods in Kubernetes

## 📌 Overview
A **Pod** is the smallest and simplest deployable unit in Kubernetes. A Pod encapsulates one or more containers, storage resources, a unique network IP, and configuration options governing container execution.

In the **CKA Exam**, you must be able to:
- Quickly create single-container and multi-container Pods.
- Extract, inspect, and troubleshoot Pod logs, events, and statuses.
- Use imperative commands to generate manifests on the fly.
- Configure port mappings, environment variables, commands, and arguments.

---

## 📂 Subdirectories in this Module

| Subdirectory | Topic | Description |
|---|---|---|
| [`01-SingleContainer`](./01-SingleContainer/README.md) | **Single Container Specs & Lifecycles** | Entrypoints/CMD overrides, running, completed, error, CrashLoopBackOff, live logs. |
| [`02-MultiContainerPod`](./02-MultiContainerPod/README.md) | **Multi-Container Pod Patterns** | Shared networking (`localhost`), shared volumes, sidecars, adapters. |
| [`03-Veriables`](./03-Veriables/README.md) | **Environment Variables** | Plain key-values, ConfigMap/Secret refs, and Downward API metadata injection. |
| [`04-DefaultPodStorage`](./04-DefaultPodStorage/README.md) | **Default Pod Storage & Ephemeral Layer** | Writable container layer, data loss on restart, volume persistence comparison. |

---

## 🚀 CKA Fast Imperative Commands

Save precious exam time using fast `kubectl` imperative commands with `--dry-run=client -o yaml`:

```bash
# Set alias and auto-completion (standard exam setup)
alias k=kubectl
export do="--dry-run=client -o yaml"

# Generate a single-container pod manifest
k run my-pod --image=nginx --port=80 $do > pod-definition.yml

# Run a pod directly
k run nginx-pod --image=nginx

# Run pod with custom labels
k run my-pod --image=nginx -l app=my-app,type=front-end

# Run a temporary pod to test networking / DNS
k run test-pod --image=busybox:1.28 --rm -it -- restart=Never -- sh
```

---

## 📄 Manifest Examples

### 1. Single Container Pod (`pod-definition.yml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
    type: front-end
spec:
  containers:
  - name: c001
    image: nginx
    ports:
    - containerPort: 80
```

### 2. Multi-Container Pod (`pod-multi-container-definition.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
    type: front-end
spec:
  containers:
  - name: c001
    image: nginx
    ports:
    - containerPort: 80
  - name: c002
    image: redis
    ports:
    - containerPort: 6379
```

---

## 🛠️ Step-by-Step Practical Exercise

1. **Create the Pod**:
   ```bash
   kubectl apply -f pod-definition.yml
   ```

2. **Verify Pod Status & Node Placement**:
   ```bash
   kubectl get pods -o wide
   kubectl get pods -l app=my-app
   ```

3. **Inspect Pod Details & Events**:
   ```bash
   kubectl describe pod my-pod
   ```

4. **Access Container Logs**:
   ```bash
   # Single container
   kubectl logs my-pod

   # Multi-container pod (must specify container with -c)
   kubectl logs my-pod -c c001
   kubectl logs my-pod -c c002
   ```

5. **Execute Commands Inside Pod**:
   ```bash
   kubectl exec -it my-pod -c c001 -- /bin/sh
   ```

6. **Delete Pod**:
   ```bash
   kubectl delete pod my-pod
   # Fast force delete (if pod is stuck):
   kubectl delete pod my-pod --force --grace-period=0
   ```

---

## 💡 CKA Exam Tips & Gotchas
- **Container specification**: `containers` is an array under `spec`, while `image` and `name` are mandatory fields.
- **Multi-container logging**: When checking logs or executing into a multi-container Pod, always supply `-c <container-name>`.
- **Command & Args override**:
  - `command` in K8s overrides Docker `ENTRYPOINT`.
  - `args` in K8s overrides Docker `CMD`.

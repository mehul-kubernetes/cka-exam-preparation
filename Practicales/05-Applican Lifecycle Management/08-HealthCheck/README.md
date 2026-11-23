# 08 - Container Health Checks (Probes)

## 📌 Overview
In Kubernetes, **Health Checks (Probes)** are diagnostic actions performed periodically by the `kubelet` on a container. Probes ensure high availability, automatic self-healing, and zero-downtime routing.

Container Health Checks are a core component of the **CKA Exam** under **Application Lifecycle Management**.

---

## 🔍 The Three Probe Types

| Probe Type | Purpose | Action When Failed | Does It Restart Pod? |
|---|---|---|:---:|
| **[Liveness Probe](./01-LivenessProbe/README.md)** | Checks if the application inside the container is running and healthy. Catches deadlocks or unrecoverable states. | Kubelet **kills** the container and applies the Pod's `restartPolicy`. | **YES** |
| **[Readiness Probe](./02-ReadinessProbe/README.md)** | Checks if the container is ready to accept incoming network traffic. | Kubelet removes the Pod IP from matching **Service Endpoints**. | **NO** |
| **[Startup Probe](./03-StartupProbe/README.md)** | Checks if a slow-starting application has finished its initialization. Disables Liveness and Readiness probes until it succeeds. | Kubelet **kills** the container if it fails to start within the configured grace period. | **YES** (if timeout exceeded) |

---

## 🛠️ The Three Probe Mechanisms (Handlers)

1. **`httpGet`**: Performs an HTTP `GET` request against the container's IP on a specified port and path.
   - Any HTTP status code between `200` and `399` indicates **Success**.
   - Any other code (e.g. `404`, `500`) or connection failure indicates **Failure**.
2. **`tcpSocket`**: Attempts to open a TCP connection to the container on a specified port.
   - If the socket connection succeeds, the probe is considered **Success**.
3. **`exec`**: Executes an arbitrary command inside the container.
   - Exit code `0` indicates **Success**.
   - Any non-zero exit code indicates **Failure**.
4. **`grpc`** (K8s 1.24+): Performs a remote procedure call using the gRPC Health Checking Protocol.

---

## ⚙️ Common Probe Parameters

| Field | Default | Description |
|---|:---:|---|
| `initialDelaySeconds` | `0` | Seconds to wait after the container starts before initiating probes. |
| `periodSeconds` | `10` | Frequency (in seconds) of performing the probe check. |
| `timeoutSeconds` | `1` | Seconds after which probe times out and is considered failed. |
| `successThreshold` | `1` | Minimum consecutive successes required to be considered healthy after a failure. (Must be `1` for liveness and startup). |
| `failureThreshold` | `3` | Consecutive failures before taking action (restart or remove from endpoints). |

---

## 📂 Subdirectories Navigation

- 💓 **[01-LivenessProbe](./01-LivenessProbe/README.md)**: Detecting application deadlocks and automatic container restarts.
- 🚦 **[02-ReadinessProbe](./02-ReadinessProbe/README.md)**: Managing Service Endpoints and eliminating routing to unready pods.
- 🚀 **[03-StartupProbe](./03-StartupProbe/README.md)**: Handling legacy, slow-starting applications without premature restarts.

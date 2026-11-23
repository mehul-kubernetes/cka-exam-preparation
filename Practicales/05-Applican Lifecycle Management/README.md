# 05 - Application Lifecycle Management

## 📌 Overview
The **Application Lifecycle Management** domain covers deploying, updating, configuring, and scaling containerized applications in Kubernetes. It represents approximately **15%** of the CKA exam syllabus.

---

## 📂 Subtopics in this Module

| Subdirectory | Topic | Key Focus |
|---|---|---|
| [`01-RollingUpdateAndRoleback`](./01-RollingUpdateAndRoleback/README.md) | **Rolling Updates & Rollbacks** | Zero-downtime updates, `kubectl set image`, `rollout status/history/undo`. |
| [`02-DeploymentStretegy`](./02-DeploymentStretegy/README.md) | **Deployment Strategies** | `RollingUpdate` (`maxSurge`, `maxUnavailable`) vs `Recreate`. |
| [`03-ConfigureVeriables`](./03-ConfigureVeriables/README.md) | **Configuring Variables** | Pod environment variables, Downward API (`fieldRef`, `resourceFieldRef`). |
| [`04-ConfigMap`](./04-ConfigMap/README.md) | **ConfigMaps** | Storing and injecting non-confidential configuration into pods. |
| [`05-Secrets`](./05-Secrets/README.md) | **Secrets** | Base64 confidential keys, TLS certificates, private registry credentials. |
| [`06-SidecarContainer`](./06-SidecarContainer/README.md) | **Sidecar Containers** | Logging, proxying, and adapter containers alongside primary apps. |
| [`07-InitContainer`](./07-InitContainer/README.md) | **InitContainers** | Pre-initialization routines, blocking start until dependencies are available. |
| [`08-HealthCheck`](./08-HealthCheck/README.md) | **Health Checks (Probes)** | Liveness, Readiness, and Startup probes (`httpGet`, `exec`, `tcpSocket`). |

# 01 - Core Concepts

## 📌 Overview
The **Core Concepts** domain represents the foundational building blocks of Kubernetes. In the CKA exam, speed and familiarity with these resources is essential for completing questions under tight time constraints.

---

## 📂 Subtopics in this Module

| Directory | Resource | Description |
|---|---|---|
| [`01-Pod`](./01-Pod/README.md) | **Pod** | Smallest deployable unit, container ports, environment variables. |
| ↳ [`02-MultiContainerPod`](./01-Pod/02-MultiContainerPod/README.md) | **Multi-Container Pod** | Shared networking (`localhost`), shared volumes, sidecar patterns. |
| [`02-ReplicationController`](./02-ReplicationController/README.md) | **ReplicationController** | Legacy controller, equality-based label selectors, self-healing. |
| [`03-Replicaset`](./03-Replicaset/README.md) | **ReplicaSet** | Next-generation replica controller with set-based selectors (`matchExpressions`). |
| [`04-Deployment`](./04-Deployment/README.md) | **Deployment** | Declarative updates, rolling rollouts, undo/history, pause/resume. |
| ↳ [`01-Deployment`](./04-Deployment/01-Deployment/README.md) | **Manifest Specs** | Deployment definition, resource constraints, labels. |
| ↳ [`02-DeploymentStrategy`](./04-Deployment/02-DeploymentStrategy/README.md) | **Strategies** | `RollingUpdate` (`maxSurge`, `maxUnavailable`) vs `Recreate`. |
| [`05-Service`](./05-Service/README.md) | **Service** | Microservice networking: `ClusterIP`, `NodePort`, `LoadBalancer`, and Endpoints. |
| [`06-Namespace`](./06-Namespace/README.md) | **Namespace** | Logical isolation, resource boundaries, and context default namespace configuration. |

---

## ⚡ Fast Imperative Quick-Reference
```bash
# Pod
k run nginx --image=nginx --port=80

# Deployment
k create deployment webapp --image=nginx --replicas=3

# Service
k expose deployment webapp --port=80 --target-port=80 --type=NodePort

# Namespace
k create ns dev
k config set-context --current --namespace=dev
```

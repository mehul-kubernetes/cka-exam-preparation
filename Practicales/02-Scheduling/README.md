# 02 - Pod Scheduling

## 📌 Overview
The **Scheduling** domain covers how Kubernetes determines which worker node should host a Pod. In the CKA exam, scheduling accounts for ~15% of your score and is critical for troubleshooting unscheduled (`Pending`) pods.

---

## 📂 Subtopics in this Module

| Subdirectory | Topic | Key Focus |
|---|---|---|
| [`01-Labels`](./01-Labels/README.md) | **Labels** | Metadata tagging on pods and nodes (`kubectl label`). |
| [`02-Selector`](./02-Selector/README.md) | **Selectors** | Equality and set-based filtering expressions (`-l`). |
| [`03-TaintsAndTolerations`](./03-TaintsAndTolerations/README.md) | **Taints & Tolerations** | Node repelling, `NoSchedule`, `NoExecute`, pod toleration specs. |
| [`04-PodAffinity`](./04-PodAffinity/README.md) | **Pod Affinity & Anti-Affinity** | Inter-pod co-location (`podAffinity`) and high-availability spreading (`podAntiAffinity`). |
| [`05-NodeAffinity`](./05-NodeAffinity/README.md) | **Node Affinity** | Hard (`required...`) and soft (`preferred...`) placement rules. |
| [`06-ResourceLimit`](./06-ResourceLimit/README.md) | **Resource Requests & Limits** | CPU/Memory requests, limits, OOMKilled (Exit Code 137), LimitRanges. |
| [`07-StaticPod`](./07-StaticPod/README.md) | **Static Pods** | Kubelet-managed control-plane pods (`/etc/kubernetes/manifests`). |
| [`08-DaemonSet`](./08-DaemonSet/README.md) | **DaemonSets** | Node-level agents (monitoring, logging, storage), DaemonSet manifests. |
| [`09-MultipleSchedulers`](./09-MultipleSchedulers/README.md) | **Custom Schedulers** | Deploying secondary schedulers, `spec.schedulerName`. |

---

## 💡 Troubleshooting Pending Pods Checklist
1. `kubectl describe pod <pod-name>`: Read the **Events** section.
2. Check for `0/X nodes available: X Insufficient cpu/memory` $\to$ adjust resource requests.
3. Check for `0/X nodes available: X node(s) had untolerated taint` $\to$ add toleration or remove taint.
4. Check for `0/X nodes available: X node(s) didn't match Pod's node affinity/selector` $\to$ verify node labels.

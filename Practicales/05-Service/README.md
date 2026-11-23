# 05 - Kubernetes Services (ClusterIP, NodePort, LoadBalancer)

## 📌 Overview
A **Service** in Kubernetes is an abstraction that defines a logical set of Pods and a policy by which to access them (micro-service routing).

Services provide persistent IP addresses and DNS names that stay constant even as underlying Pods are terminated, restarted, or rescheduled.

---

## 🔑 Service Types

| Type | Accessibility | Description | Default Port Range |
|---|---|---|---|
| **ClusterIP** (Default) | Internal only | Exposes service on a cluster-internal IP. Reachable only within the cluster. | N/A |
| **NodePort** | External & Internal | Exposes service on each Node’s IP at a static port. | `30000-32767` |
| **LoadBalancer** | External | Uses cloud provider's external load balancer (AWS ELB, GCP Cloud LB, Azure LB). | Provider specific |
| **ExternalName** | Internal to External | Maps service to a DNS name (e.g., `db.example.com`). | N/A |

---

## 🚀 CKA Fast Imperative Commands

```bash
# 1. Expose existing pod/deployment as ClusterIP
kubectl expose deployment nginx-deploy --port=80 --target-port=80 --name=nginx-service

# 2. Expose pod/deployment as NodePort
kubectl expose pod nginx-pod --type=NodePort --port=80 --name=nginx-np

# 3. Fast generation using create service
kubectl create service clusterip my-cs --tcp=80:8080 --dry-run=client -o yaml > 02-clusterip.yaml
kubectl create service nodeport my-np --tcp=80:80 --node-port=30080 --dry-run=client -o yaml > 01-nodeport.yaml
kubectl create service loadbalancer my-lb --tcp=80:80 --dry-run=client -o yaml > 03-loadbalancer.yaml
```

---

## 📄 Manifest Examples

### 1. NodePort (`01-nodeport.yaml`)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 80           # Port exposed inside cluster
      targetPort: 80     # Port container listens on
      nodePort: 30080    # Port on worker node (range: 30000-32767)
```

### 2. ClusterIP (`02-clusterip.yaml`)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

### 3. LoadBalancer (`03-loadbalancer.yaml`)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

## 🔍 Verification & Endpoints Debugging

```bash
# Check service and allocated Cluster-IP and NodePort
kubectl get svc -o wide

# Check Endpoints (Pods successfully targeted by service)
kubectl get endpoints my-nodeport-service
kubectl get ep

# If Endpoints is <none>, check:
# 1. Do pod labels match spec.selector?
# 2. Is pod running and ready?
# 3. Does targetPort match containerPort?

# Test internal connection via DNS
kubectl run test --image=busybox --rm -it -- restart=Never -- wget -qO- http://my-clusterip-service
```

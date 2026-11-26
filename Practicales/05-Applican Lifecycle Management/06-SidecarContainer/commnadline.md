kubectl get pod [podname]

# Sidecar Container Check "pod-sidecar-container2"
kubectl exec pod-sidecar-container2 -c db-checker -- pg_isready -h localhost -p 5432 -U postgres
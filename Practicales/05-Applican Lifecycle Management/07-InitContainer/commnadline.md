kubectl get pod

# Check Init Container

kubectl logs pod-init-container1
kubectl exec -it pod-init-container1 -- sh
kubectl exec pod-init-container1 -c main-app -- cat /work/message.txt
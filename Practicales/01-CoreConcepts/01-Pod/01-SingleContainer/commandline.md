kubectl get pod
kubectl get pod -o wide
kubectl apply -f file.yaml
kubectl log <podname>
kubectl log -f <podname>
kubectl run mypod --image=nginx --dry-run=client -o yaml > mypod.yaml
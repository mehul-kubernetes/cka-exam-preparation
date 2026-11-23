# Create Pod
kubectl get pod

kubectl get pod -o wide

kubectl apply -f file.yaml

kubectl replace -f file.yaml

kubectl log [podname]

kubectl log -f [podname]

kubectl exec -it [podname] -- /bin/bash

kubectl exec -it [podname] -c [containername] -- /bin/sh

kubectl run mypod --image=nginx --dry-run=client -o yaml > mypod.yaml

# Edit Pod
kubectl edit pod [podname]

# Access Pod
kubectl port-forward --address 0.0.0.0 pod/[podname] 8080:80

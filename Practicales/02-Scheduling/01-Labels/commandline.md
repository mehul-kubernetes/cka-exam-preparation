# Lables On Pods

kubectl get pods --show-labels

kubectl label pod [podname] tier=web

kubectl get pods -l app=web

# Labels On Node

kubectl label node my-cluster-worker web=dev

ubectl label pod [nodename] tier=web

kubectl get node -l tier=web
kubectl get pods --show-labels

kubectl label pod [podname] tier=web

kubectl get pods -l app=web
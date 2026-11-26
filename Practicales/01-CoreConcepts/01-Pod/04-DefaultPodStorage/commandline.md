kubectl run pod-default-storage --image=busybox --command -- sleep 3600 

kubectl exec pod-default-storage -- sh -c 'echo hello-from-container > /tmp/message.txt'

kubectl exec pod-default-storage -- cat /tmp/message.txt
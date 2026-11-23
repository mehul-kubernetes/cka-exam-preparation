
#!/bin/bash

# ==========================================
# 1. UPDATE UBUNTU
# ==========================================

sudo apt update
sudo apt upgrade -y

# ==========================================
# 2. INSTALL DOCKER
# ==========================================

sudo apt install -y ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable --now docker

# Verify Docker
docker --version
sudo docker run hello-world

# ==========================================
# 3. CONFIGURE DOCKER PERMISSIONS
# ==========================================

sudo usermod -aG docker $USER
newgrp docker

# Verify Docker
docker ps

# ==========================================
# 4. INSTALL KUBECTL
# ==========================================

sudo apt install -y curl

KUBECTL_VERSION=$(curl -L -s https://dl.k8s.io/release/stable.txt)

curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Verify
kubectl version --client

# ==========================================
# 5. INSTALL MINIKUBE
# ==========================================

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Verify
minikube version

# ==========================================
# 6. START MINIKUBE CLUSTER
# ==========================================

minikube start \
  --driver=docker \
  --cpus=4 \
  --memory=4096 \
  --disk-size=20g

# ==========================================
# 7. VERIFY KUBERNETES CLUSTER
# ==========================================

minikube status

kubectl cluster-info

kubectl get nodes -o wide

kubectl get pods -A

docker ps

# ==========================================
# 8. DEPLOY NGINX APPLICATION
# ==========================================

kubectl create deployment nginx --image=nginx

kubectl get deployments

kubectl get pods -o wide

# ==========================================
# 9. EXPOSE NGINX SERVICE
# ==========================================

kubectl expose deployment nginx \
  --port=80 \
  --target-port=80 \
  --type=NodePort

# Verify
kubectl get svc

# ==========================================
# 10. ACCESS NGINX APPLICATION
# ==========================================

minikube service nginx --url

# Alternative
kubectl port-forward svc/nginx 8080:80

# Access:
# http://localhost:8080

# ==========================================
# 11. ENABLE INGRESS ADDON
# ==========================================

minikube addons enable ingress

# Verify ingress
kubectl get pods -n ingress-nginx

kubectl get ingressclass

# ==========================================
# 12. CREATE SAMPLE INGRESS
# ==========================================

cat <<EOF > nginx-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: nginx.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: nginx
                port:
                  number: 80
EOF

kubectl apply -f nginx-ingress.yaml

kubectl get ingress

# ==========================================
# 13. CHECK MINIKUBE IP
# ==========================================

minikube ip

# ==========================================
# 14. MINIKUBE MANAGEMENT
# ==========================================

# Check status
minikube status

# Stop cluster
minikube stop

# Start cluster
minikube start

# Pause cluster
minikube pause

# Unpause cluster
minikube unpause

# List profiles
minikube profile list

# Open dashboard
minikube dashboard

# SSH into Minikube node
minikube ssh

# ==========================================
# 15. DELETE MINIKUBE CLUSTER
# ==========================================

minikube delete

# Verify
minikube profile list


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
sudo docker run hello-world

# ==========================================
# 3. CONFIGURE DOCKER PERMISSIONS
# ==========================================
sudo usermod -aG docker $USER
newgrp docker

# Verify
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
# 5. INSTALL K3D
# ==========================================
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash

# Verify
k3d version

# ==========================================
# 6. CREATE K3D CLUSTER
# ==========================================
k3d cluster create cka-k3d \
  --servers 1 \
  --agents 2

# ==========================================
# 7. VERIFY CLUSTER
# ==========================================
k3d cluster list
k3d node list
kubectl cluster-info
kubectl get nodes -o wide
kubectl get pods -A
docker ps

# ==========================================
# 8. DEPLOY NGINX
# ==========================================
kubectl create deployment nginx --image=nginx

kubectl get deployments
kubectl get pods -o wide

# ==========================================
# 9. EXPOSE NGINX
# ==========================================
kubectl expose deployment nginx \
  --port=80 \
  --target-port=80 \
  --type=NodePort

kubectl get svc

# ==========================================
# 10. TEST NGINX
# ==========================================
kubectl run curl-test \
  --image=curlimages/curl:latest \
  --restart=Never -it --rm -- curl nginx

# ==========================================
# 11. STOP / START CLUSTER
# ==========================================
k3d cluster stop cka-k3d
k3d cluster start cka-k3d

# ==========================================
# 12. DELETE CLUSTER
# ==========================================
k3d cluster delete cka-k3d


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

# Verify
docker ps

# ==========================================
# 4. INSTALL KUBECTL
# ==========================================

KUBECTL_VERSION=$(curl -L -s https://dl.k8s.io/release/stable.txt)

curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Verify
kubectl version --client

# ==========================================
# 5. INSTALL KIND
# ==========================================

[ "$(uname -m)" = "x86_64" ] && ARCH=amd64
[ "$(uname -m)" = "aarch64" ] && ARCH=arm64

curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-${ARCH}

chmod +x ./kind

sudo mv ./kind /usr/local/bin/kind

# Verify
kind version

# ==========================================
# 6. CREATE KIND CLUSTER CONFIGURATION
# ==========================================

cat <<EOF > kind-cluster.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 80
        hostPort: 80
        protocol: TCP
      - containerPort: 443
        hostPort: 443
        protocol: TCP

  - role: worker

  - role: worker
EOF

# View configuration
cat kind-cluster.yaml

# ==========================================
# 7. CREATE KIND MULTI-NODE CLUSTER
# ==========================================

kind create cluster \
  --name cka-kind \
  --config kind-cluster.yaml

# ==========================================
# 8. VERIFY CLUSTER
# ==========================================

kind get clusters
kind get nodes

kubectl cluster-info
kubectl get nodes -o wide
kubectl get pods -A

docker ps

# ==========================================
# 9. DEPLOY NGINX APPLICATION
# ==========================================

kubectl create deployment nginx --image=nginx

kubectl get deployments
kubectl get pods -o wide

# ==========================================
# 10. EXPOSE NGINX USING NODEPORT
# ==========================================

kubectl expose deployment nginx \
  --port=80 \
  --target-port=80 \
  --type=NodePort \
  --name=nginx

# ==========================================
# 11. CHECK SERVICE
# ==========================================

kubectl get svc
kubectl describe svc nginx

# ==========================================
# 12. LOAD LOCAL DOCKER IMAGE
# ==========================================

kind load docker-image nginx:latest --name cka-kind

# ==========================================
# 13. CLUSTER MANAGEMENT
# ==========================================

# List clusters
kind get clusters

# List nodes
kind get nodes

# Export kubeconfig
kind export kubeconfig --name cka-kind

# ==========================================
# 14. DELETE CLUSTER
# ==========================================

kind delete cluster --name cka-kind

# Verify deletion
kind get clusters

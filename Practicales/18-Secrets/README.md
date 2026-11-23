# 18 - Secrets

## 📌 Overview
A **Secret** is an object that contains a small amount of sensitive data such as a password, a token, or a key. Using a Secret means that you don't need to include confidential data in your application code or container image.

> [!IMPORTANT]
> By default, Kubernetes Secrets are stored unencrypted as base64-encoded strings in `etcd`. In production, enable **Encryption at Rest** in `kube-apiserver`.

---

## 🚀 CKA Fast Imperative Commands

```bash
# 1. Create generic secret from literals
kubectl create secret generic app-secret \
  --from-literal=DB_Host=mysql \
  --from-literal=DB_User=root \
  --from-literal=DB_Password=password123

# 2. Create generic secret from file
kubectl create secret generic ssl-key --from-file=ssh-privatekey=/path/to/id_rsa

# 3. Create TLS secret
kubectl create secret tls tls-secret --cert=tls.crt --key=tls.key

# 4. Create Docker Registry secret
kubectl create secret docker-registry regcred \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=myuser \
  --docker-password=mypassword \
  --docker-email=myuser@example.com

# 5. Generate Secret YAML
kubectl create secret generic my-secret --from-literal=token=xyz --dry-run=client -o yaml > secret.yaml
```

---

## 📄 Manifest Examples

### 1. Secret Definition (`secret-data.yaml`)
Values must be base64-encoded:
```bash
echo -n "mysql" | base64        # bXlzcWw=
echo -n "root" | base64         # cm9vdA==
echo -n "password123" | base64  # cGFzc3dvcmQxMjM=
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  DB_Host: bXlzcWw=
  DB_User: cm9vdA==
  DB_Password: cGFzc3dvcmQxMjM=
```
*(Alternatively, use `stringData` to supply plaintext strings, which K8s will automatically encode).*

### 2. Pod Consuming Secret (`pod-definition-secret-definition.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-pod
spec:
  containers:
  - name: app
    image: nginx
    envFrom:
    - secretRef:
        name: app-secret
```

### 3. Consuming Secret as a Volume Mount
```yaml
volumes:
- name: secret-vol
  secret:
    secretName: app-secret
```

---

## 🛠️ Verification & Decoding

```bash
# View secret (encoded)
kubectl get secret app-secret -o yaml

# Decode specific secret field directly via kubectl and base64
kubectl get secret app-secret -o jsonpath='{.data.DB_Password}' | base64 --decode
```

---

## 💡 CKA Exam Tips
- Remember that `echo -n` prevents adding a trailing newline, which could corrupt database passwords or API tokens!

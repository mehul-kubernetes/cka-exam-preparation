# 01 - Authentication and Authorization

## 📌 Overview
Every request to the Kubernetes API server undergoes three stages:
1. **Authentication (AuthN)**: Verifies the requester's identity (User, Group, or ServiceAccount).
2. **Authorization (AuthZ)**: Verifies if the authenticated entity has permission to perform the requested verb (`get`, `list`, `create`, `delete`) on the resource (`pods`, `deployments`, `nodes`).
3. **Admission Control**: Validates or mutates the resource request (e.g., ResourceQuota, LimitRanger, PodSecurityAdmission).

---

## 🔑 CertificateSigningRequest (CSR) Workflow

Kubernetes uses an internal CA to sign user certificates:

1. **User generates private key and CSR**:
   ```bash
   openssl genrsa -out john.key 2048
   openssl req -new -key john.key -out john.csr -subj "/CN=john/O=developers"
   ```

2. **Create K8s `CertificateSigningRequest` object**:
   ```yaml
   apiVersion: certificates.k8s.io/v1
   kind: CertificateSigningRequest
   metadata:
     name: john-developer
   spec:
     request: <BASE64_ENCODED_CSR>
     signerName: kubernetes.io/kube-apiserver-client
     usages:
     - client auth
   ```

3. **Admin approves CSR**:
   ```bash
   kubectl get csr
   kubectl certificate approve john-developer
   ```

4. **Extract signed certificate**:
   ```bash
   kubectl get csr john-developer -o jsonpath='{.status.certificate}' | base64 --decode > john.crt
   ```

---

## 🔍 Authorization Testing (`kubectl auth can-i`)

Test permissions without switching identities:
```bash
# Can I list pods?
kubectl auth can-i list pods

# Can user 'john' create deployments in default namespace?
kubectl auth can-i create deployments --as=john --namespace=default

# Can ServiceAccount 'app-sa' in namespace 'dev' delete secrets?
kubectl auth can-i delete secrets --as=system:serviceaccount:dev:app-sa -n dev
```

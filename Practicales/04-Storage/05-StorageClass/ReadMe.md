# 14 - StorageClass & Dynamic Volume Provisioning

## 📌 Overview
A **StorageClass** provides a way for administrators to describe the "classes" of storage they offer. Without StorageClasses, administrators must manually pre-provision PersistentVolumes (static provisioning).

With a **StorageClass**, when a user creates a PVC, Kubernetes automatically and dynamically provisions the underlying storage volume and creates the corresponding PersistentVolume.

---

## 🔑 Key Parameters in StorageClass

| Parameter | Options / Examples | Explanation |
|---|---|---|
| `provisioner` | `kubernetes.io/no-provisioner`, `rancher.io/local-path`, `ebs.csi.aws.com` | Defines which volume plugin is used for provisioning PVs. |
| `reclaimPolicy` | `Delete` (default), `Retain` | Whether dynamic PV is deleted or retained when PVC is deleted. |
| `volumeBindingMode` | `Immediate` vs `WaitForFirstConsumer` | `Immediate`: PV created immediately when PVC created. <br>`WaitForFirstConsumer`: Volume binding delayed until Pod using PVC is scheduled (respects Pod topology and node affinity). |
| `allowVolumeExpansion` | `true`, `false` | Enables resizing PVC storage dynamically. |

---

## 📄 Manifest Examples

### 1. StorageClass Definition (`mystorage-class-local.yaml`)
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: mylocal-storage
provisioner: rancher.io/local-path
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

### 2. PVC Requesting StorageClass (`mypvc-sc.yaml`)
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc-sc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: mylocal-storage
  resources:
    requests:
      storage: 2Gi
```

### 3. Pod Mounting the PVC (`mypod-sc.yaml`)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod-sc
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: data
      mountPath: /data
  volumes:
  - name: data
    persistentVolumeClaim:
      claimName: mypvc-sc
```

---

## 🛠️ Step-by-Step Hands-On Exercise

1. **Deploy StorageClass**:
   ```bash
   kubectl apply -f mystorage-class-local.yaml
   kubectl get sc
   ```

2. **Create PVC**:
   ```bash
   kubectl apply -f mypvc-sc.yaml
   kubectl get pvc
   # Note: If volumeBindingMode is WaitForFirstConsumer, status will be Pending until a Pod is created.
   ```

3. **Deploy Pod**:
   ```bash
   kubectl apply -f mypod-sc.yaml
   kubectl get pod mypod-sc -w
   ```

4. **Verify Dynamic PV Creation**:
   ```bash
   # Notice a PV was automatically created and bound!
   kubectl get pv
   kubectl get pvc
   ```

5. **Set Default StorageClass (Common Exam Task)**:
   ```bash
   kubectl patch storageclass mylocal-storage -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
   ```

6. **Clean up**:
   ```bash
   kubectl delete -f mypod-sc.yaml
   kubectl delete -f mypvc-sc.yaml
   kubectl delete -f mystorage-class-local.yaml
   ```

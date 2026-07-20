# 🚀 Kubernetes Pod with Dynamic Persistent Volume Claim (PVC)

> Complete Interview Notes + Production Concepts + Hands-on Demo

---

# 📑 Table of Contents

1. Introduction
2. Why Pod Needs Persistent Storage?
3. What is Dynamic PVC?
4. Architecture
5. Workflow
6. Pod YAML Explanation
7. Deployment Steps
8. Verification
9. Data Persistence Demo
10. Production Use Cases
11. Advantages
12. Common Mistakes
13. Troubleshooting
14. Important Commands
15. Interview Questions
16. Summary

---

# 1️⃣ Introduction

By default, Kubernetes Pods are **stateless**.

If a Pod is deleted, all data stored inside the container is also deleted.

To avoid data loss, Kubernetes allows Pods to mount a **PersistentVolumeClaim (PVC)**.

This Pod uses a **Dynamic PVC**, meaning the Persistent Volume (PV) was automatically created by the StorageClass.

---

# 2️⃣ Why Pod Needs Persistent Storage?

Without Persistent Storage

```
Pod

↓

Container

↓

Data

↓

Pod Deleted

↓

Data Lost ❌
```

With Persistent Storage

```
Pod

↓

PVC

↓

PV

↓

Storage

↓

Pod Deleted

↓

Data Still Exists ✅
```

---

# 3️⃣ What is Dynamic PVC?

Dynamic PVC means

- Developer creates only a PVC.
- StorageClass automatically provisions a PV.
- Pod consumes the PVC.
- No manual PV creation is required.

Workflow

```
Developer

↓

Create PVC

↓

StorageClass

↓

Provisioner

↓

PV Created Automatically

↓

PVC Bound

↓

Pod Uses PVC
```

---

# 4️⃣ Architecture

```
                Kubernetes Cluster

              +------------------+

                    nginx Pod

                       │

             Volume Mount

/usr/share/nginx/html

                       │

                 PersistentVolumeClaim

                 dynamic-pvc

                       │

                StorageClass

                  standard

                       │

          Minikube HostPath Provisioner

                       │

          Persistent Volume (Auto Created)

                       │

              Host Machine Storage
```

---

# 5️⃣ Workflow

```
Create PVC

↓

StorageClass Triggered

↓

PV Automatically Created

↓

PVC Bound

↓

Pod Created

↓

PVC Mounted

↓

Application Reads/Writes Data

↓

Pod Deleted

↓

Data Remains Safe
```

---

# 6️⃣ Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod1
  namespace: default

spec:
  containers:
    - name: nginx
      image: nginx:latest

      volumeMounts:
        - name: my-volume
          mountPath: /usr/share/nginx/html

  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: dynamic-pvc
```

---

# 7️⃣ YAML Explanation

## apiVersion

```yaml
apiVersion: v1
```

Uses Kubernetes Core API.

---

## kind

```yaml
kind: Pod
```

Creates a Pod.

---

## metadata

```yaml
metadata:
  name: nginx-pod1
```

Pod name.

---

## namespace

```yaml
namespace: default
```

Pod will be created inside the **default namespace**.

---

## containers

```yaml
containers:
```

Defines the list of containers running inside the Pod.

---

## image

```yaml
image: nginx:latest
```

Runs the latest Nginx container image.

---

## volumeMounts

```yaml
volumeMounts:
```

Mounts a Kubernetes Volume inside the container.

---

## name

```yaml
name: my-volume
```

Logical name of the mounted volume.

It must match the name under **volumes**.

---

## mountPath

```yaml
mountPath: /usr/share/nginx/html
```

The Persistent Volume is mounted here.

Nginx serves files from this directory.

Example

```
index.html

demo.html

images

css
```

Everything stored here goes to the Persistent Volume.

---

## volumes

```yaml
volumes:
```

Defines Kubernetes Volumes available to the Pod.

---

## persistentVolumeClaim

```yaml
persistentVolumeClaim:
```

Instead of using a PV directly,

Pod uses a PVC.

---

## claimName

```yaml
claimName: dynamic-pvc
```

Pod requests storage through this PVC.

Flow

```
Pod

↓

dynamic-pvc

↓

Persistent Volume

↓

Storage
```

---

# 8️⃣ Deployment

Create Pod

```bash
kubectl apply -f pod.yaml
```

Verify

```bash
kubectl get pods
```

Describe

```bash
kubectl describe pod nginx-pod1
```

---

# 9️⃣ Verify Mounted Storage

Login

```bash
kubectl exec -it nginx-pod1 -- sh
```

Go

```bash
cd /usr/share/nginx/html
```

Create File

```bash
echo "Hello Dynamic PVC" > index.html
```

Check

```bash
cat index.html
```

Exit

```bash
exit
```

---

# 10️⃣ Data Persistence Demo

Delete Pod

```bash
kubectl delete pod nginx-pod1
```

Recreate

```bash
kubectl apply -f pod.yaml
```

Login Again

```bash
kubectl exec -it nginx-pod1 -- sh
```

Verify

```bash
cat /usr/share/nginx/html/index.html
```

Output

```
Hello Dynamic PVC
```

This proves the data is stored in the Persistent Volume, not inside the container.

---

# 11️⃣ Production Use Cases

- Nginx Website Content
- Apache Web Server
- WordPress Uploads
- Jenkins Home Directory
- SonarQube Data
- GitLab Storage
- Shared Application Files
- Log Storage
- Configuration Files
- Static Website Hosting

---

# 12️⃣ Advantages

- Persistent Data
- Automatic Storage Provisioning
- No Manual PV Creation
- Easy Scaling
- Production Ready
- Kubernetes Native
- Easy Backup

---

# 13️⃣ Common Mistakes

❌ PVC does not exist

```
claimName incorrect
```

---

❌ Wrong StorageClass

PVC remains Pending.

---

❌ Wrong Mount Path

Application cannot read files.

---

❌ Namespace mismatch

Pod cannot locate PVC.

---

# 14️⃣ Troubleshooting

Check PVC

```bash
kubectl get pvc
```

Check PV

```bash
kubectl get pv
```

Describe Pod

```bash
kubectl describe pod nginx-pod1
```

Events

```bash
kubectl get events
```

Logs

```bash
kubectl logs nginx-pod1
```

---

# 15️⃣ Interview Questions

## Q1 Why does a Pod use PVC instead of PV directly?

Because PVC abstracts the storage details. Applications request storage through PVC, while Kubernetes handles the PV binding.

---

## Q2 What happens if the Pod is deleted?

The Pod is deleted, but the data remains in the Persistent Volume.

---

## Q3 What happens if PVC is deleted?

Depends on the **Reclaim Policy**.

If it is **Delete**, the dynamically provisioned storage is deleted.

If it is **Retain**, the storage remains.

---

## Q4 Does this Pod create a PV?

No.

The Pod only consumes an existing PVC.

The PV is created automatically by the StorageClass when the PVC is created.

---

## Q5 Can the Pod directly mount a Persistent Volume?

No.

Best practice is:

```
Pod

↓

PVC

↓

PV
```

---

## Q6 Why is volumeMount name and volumes name the same?

Because Kubernetes maps the container mount to the corresponding volume definition using the name.

---

## Q7 What is mounted at `/usr/share/nginx/html`?

The storage provided by the PVC. Any file created in this directory is stored on the Persistent Volume.

---

## Q8 What happens if the PVC is in another namespace?

The Pod cannot mount it because a Pod can only reference a PVC in the **same namespace**.

---

## 16️⃣ Summary

✔ Pod is stateless by default.

✔ PVC provides persistent storage.

✔ Dynamic provisioning automatically creates the PV.

✔ Pod mounts the PVC.

✔ Data survives Pod deletion.

✔ StorageClass handles automatic storage provisioning.

✔ This pattern is widely used for production workloads requiring persistent data.

---

# 🎯 Learning Outcome

After completing this lab, you should be able to:

- Understand how a Pod consumes a Dynamic PVC.
- Explain the relationship between Pod, PVC, PV, and StorageClass.
- Mount persistent storage inside a container.
- Verify data persistence after Pod recreation.
- Troubleshoot common storage issues.
- Confidently answer Dynamic PVC interview questions.
# 📦 Kubernetes Static Persistent Volume (PV) & Persistent Volume Claim (PVC)

> Complete Interview Notes + Hands-on Demo + Production Concepts

---

# 📑 Table of Contents

1. Introduction
2. What is Persistent Storage?
3. Why Storage is Required?
4. Ephemeral Storage vs Persistent Storage
5. What is Persistent Volume (PV)?
6. What is Persistent Volume Claim (PVC)?
7. Static Provisioning
8. Dynamic Provisioning
9. Static PV Workflow
10. Architecture
11. Folder Structure
12. Namespace YAML
13. Pod YAML
14. PV YAML
15. PVC YAML
16. Secret YAML
17. Deployment Steps
18. Verification
19. Data Persistence Demo
20. Reclaim Policies
21. Access Modes
22. Volume Modes
23. Storage Classes
24. Production Use Cases
25. Advantages
26. Disadvantages
27. Troubleshooting
28. Important kubectl Commands
29. Interview Questions
30. Summary

---

# 1️⃣ Introduction

Containers are **stateless by default**.

Whenever a Pod is deleted, all data inside the container is also deleted.

Example

```
Pod Deleted
↓

Container Deleted
↓

All Files Lost
```

To solve this problem Kubernetes provides Persistent Storage.

---

# 2️⃣ What is Persistent Storage?

Persistent Storage means

> Data remains available even after Pod restart or Pod deletion.

Instead of storing data inside the container,
the data is stored in an external storage location.

Example

```
Pod

↓

Persistent Volume

↓

Disk
```

---

# 3️⃣ Why Storage is Required?

Suppose an application stores

- User Images
- Database Files
- Logs
- Uploads
- Reports
- Backups

If Pod crashes

Everything will be lost.

Persistent Volume keeps data safe.

---

# 4️⃣ Ephemeral Storage vs Persistent Storage

| Ephemeral | Persistent |
|------------|------------|
| Deleted with Pod | Survives Pod |
| Temporary | Permanent |
| EmptyDir | PV |
| Fast | Reliable |
| Testing | Production |

---

# 5️⃣ What is Persistent Volume (PV)?

Persistent Volume is a storage resource inside Kubernetes Cluster.

It represents

- Disk
- NFS
- Azure Disk
- AWS EBS
- GCE PD
- HostPath
- SAN
- NAS

Think of PV as

```
Physical Hard Disk
```

managed by Kubernetes.

---

# 6️⃣ What is PVC?

PVC stands for

Persistent Volume Claim

It is a request for storage.

Instead of directly using PV,

Pods request storage through PVC.

Flow

```
Pod

↓

PVC

↓

PV

↓

Disk
```

---

# 7️⃣ Static Provisioning

Storage Admin manually creates PV.

Developer creates PVC.

PVC searches matching PV.

If matching found

PVC binds with PV.

---

# 8️⃣ Dynamic Provisioning

No manual PV creation.

StorageClass automatically creates storage.

Most Production Clusters use Dynamic Provisioning.

---

# 9️⃣ Static PV Workflow

```
Admin

↓

Create PV

↓

Developer

↓

Create PVC

↓

PVC finds matching PV

↓

Bound

↓

Pod uses PVC

↓

Application stores data
```

---

# 🔟 Architecture

```
                Kubernetes Cluster

        +------------------------+

               Application

                     │

                  Pod

                     │

        Volume Mount (/data)

                     │

                  PVC

                     │

          Request Storage

                     │

                   PV

                     │

              HostPath Disk

                     │

        /mnt/static-storage
```

---

# Folder Structure

```
Static/

│

├── Namespace.yaml

├── pv.yaml

├── pvc.yaml

├── pod.yaml

├── Secret.yaml

└── README.md
```

---

# Namespace

Namespace logically isolates resources.

Example

```
kubectl apply -f Namespace.yaml
```

Verify

```
kubectl get ns
```

---

# Persistent Volume

PV defines

- Storage Size
- Access Mode
- Storage Type
- Reclaim Policy
- Storage Class

Example

```
10Gi Storage

HostPath

ReadWriteOnce
```

---

# Persistent Volume Claim

PVC requests

```
10Gi

ReadWriteOnce
```

Kubernetes matches

PVC ⇄ PV

If all conditions match

Status becomes

```
Bound
```

---

# Pod

Pod never directly accesses PV.

Always uses PVC.

Example

```
spec:

volumes:

persistentVolumeClaim

↓

volumeMount

↓

/data
```

---

# Secret

Secrets store

- Password
- Username
- API Keys
- Tokens

instead of hardcoding inside YAML.

---

# Deployment Steps

## Step 1

```
kubectl apply -f Namespace.yaml
```

---

## Step 2

```
kubectl apply -f pv.yaml
```

---

## Step 3

```
kubectl apply -f pvc.yaml
```

---

## Step 4

```
kubectl get pvc
```

Expected

```
STATUS

Bound
```

---

## Step 5

```
kubectl apply -f pod.yaml
```

---

## Step 6

```
kubectl get pods
```

---

## Step 7

Verify Mount

```
kubectl exec -it pod-name -- sh

cd /data

echo Hello > demo.txt
```

Delete Pod

```
kubectl delete pod pod-name
```

Create Pod Again

```
cat /data/demo.txt
```

Output

```
Hello
```

Data still exists.

---

# Verification Commands

PV

```
kubectl get pv
```

PVC

```
kubectl get pvc
```

Describe PV

```
kubectl describe pv
```

Describe PVC

```
kubectl describe pvc
```

Mounted Volumes

```
kubectl describe pod pod-name
```

---

# Reclaim Policies

## Retain

Data remains after PVC deletion.

Production Databases

---

## Delete

Storage deleted automatically.

Cloud Storage

---

## Recycle

Deprecated

---

# Access Modes

## ReadWriteOnce (RWO)

One Node Read Write

---

## ReadOnlyMany (ROX)

Many Nodes Read Only

---

## ReadWriteMany (RWX)

Many Nodes Read Write

---

## ReadWriteOncePod

Single Pod only

---

# Volume Modes

Filesystem

Most Common

Block

Raw Block Devices

---

# StorageClass

StorageClass automates storage creation.

Without StorageClass

Static Provisioning

With StorageClass

Dynamic Provisioning

---

# Production Use Cases

✅ MySQL

✅ PostgreSQL

✅ MongoDB

✅ Redis Persistence

✅ Jenkins Home

✅ SonarQube

✅ Nexus Repository

✅ Elasticsearch

✅ Prometheus

✅ Grafana

✅ WordPress Uploads

✅ GitLab

---

# Advantages

✔ Persistent Data

✔ Decouples Storage

✔ Easy Backup

✔ Easy Migration

✔ Secure

✔ Production Ready

✔ High Availability

---

# Disadvantages

❌ Manual PV Creation

❌ Capacity Planning Required

❌ Scaling Difficult

❌ Less Flexible than Dynamic Provisioning

---

# Troubleshooting

PVC Pending

Reason

No matching PV

Fix

```
kubectl describe pvc
```

---

Pod Pending

PVC not Bound

```
kubectl get pvc
```

---

Permission Denied

Fix

Check

```
securityContext

fsGroup

runAsUser
```

---

HostPath Not Working

Check Path

```
/mnt/data
```

exists on Node.

---

# Important kubectl Commands

```
kubectl get pv

kubectl get pvc

kubectl get pods

kubectl describe pv

kubectl describe pvc

kubectl describe pod pod-name

kubectl exec -it pod-name -- sh

kubectl logs pod-name

kubectl delete pod pod-name
```

---

# Most Asked Interview Questions

## Q1 What is PV?

Cluster Storage Resource.

---

## Q2 What is PVC?

Storage Request.

---

## Q3 Who creates PV?

Cluster Administrator.

---

## Q4 Who creates PVC?

Developer/Application Team.

---

## Q5 Can Pod directly use PV?

No.

Pod always uses PVC.

---

## Q6 Difference between Static and Dynamic Provisioning?

Static

Admin creates PV manually.

Dynamic

StorageClass creates PV automatically.

---

## Q7 What happens if PVC is deleted?

Depends on Reclaim Policy.

---

## Q8 What happens if Pod is deleted?

Data remains inside PV.

---

## Q9 Can multiple Pods use same PV?

Depends on Access Mode.

---

## Q10 Why StorageClass is preferred?

Automatic provisioning.

---

## Q11 Can one PVC bind multiple PVs?

No.

One PVC binds to one PV.

---

## Q12 Can one PV bind multiple PVCs?

No.

Once Bound, PV cannot bind another PVC.

---

## Q13 What causes PVC Pending state?

- Size mismatch
- AccessMode mismatch
- StorageClass mismatch
- No available PV

---

## Q14 Which reclaim policy is safest for databases?

Retain

---

# Summary

✔ PV = Actual Storage

✔ PVC = Storage Request

✔ Pod uses PVC

✔ PVC binds PV

✔ PV stores data

✔ Data survives Pod restart

✔ Static Provisioning requires manual PV creation

✔ Dynamic Provisioning uses StorageClass

✔ Persistent Storage is mandatory for Stateful Applications

---

# 📚 Learning Outcome

After completing this project, you should be able to:

- Understand Kubernetes Persistent Storage concepts
- Explain PV and PVC in interviews
- Differentiate Static vs Dynamic Provisioning
- Configure Persistent Volumes manually
- Create and bind PVCs
- Mount storage into Pods
- Verify data persistence after Pod recreation
- Troubleshoot common PV/PVC issues
- Apply persistent storage concepts in real production environments

---
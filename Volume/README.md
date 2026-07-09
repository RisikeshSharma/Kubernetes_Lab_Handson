# Kubernetes EmptyDir Volume 

# What is EmptyDir?

`emptyDir` is a temporary storage volume provided by Kubernetes.

When a Pod is created, Kubernetes automatically creates an empty directory for that Pod.

All containers inside the same Pod can access this directory if it is mounted.

The data remains available until the Pod is deleted.

---

# Definition

> EmptyDir is a temporary shared storage volume that exists as long as the Pod exists.

It is mainly used for:

- Sharing files between containers
- Temporary cache
- Logs
- Scratch space
- Intermediate processing data

---

# How EmptyDir Works

```
                Pod Created
                     │
                     ▼
          Kubernetes Creates
             Empty Directory
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
  Container-1               Container-2
 Writes Data              Reads/Writes Data
        │                         │
        └──────────Shared─────────┘
                 EmptyDir Volume
```

---

# Lifecycle

Pod Created
        │
        ▼
EmptyDir Created
        │
        ▼
Containers Use Data
        │
        ▼
Container Restart
        │
        ▼
Data Still Exists
        │
        ▼
Pod Deleted
        │
        ▼
Data Deleted Forever

---

# Important Rule

Container Restart

❌ Data is NOT deleted

Pod Restart (same Pod)

❌ Data is NOT deleted

Pod Delete

✅ Data is deleted

Node Failure

❌ Data may be lost

---

# Where is EmptyDir Stored?

Default

Node Disk

Example

```
/var/lib/kubelet/pods/<pod-id>/volumes/
```

Depending on Kubernetes configuration.

---

# EmptyDir Types

## 1. Disk Based (Default)

```
volumes:
- name: shared-volume
  emptyDir: {}
```

Uses Node Storage.

Best for:

- Temporary files
- Cache
- Logs

---

## 2. Memory Based

```
volumes:
- name: shared-volume
  emptyDir:
    medium: Memory
```

Uses RAM instead of disk.

Advantages

- Very Fast
- Low latency

Disadvantages

- Uses Pod Memory
- Lost when Pod dies

Best for

- Cache
- High-speed processing
- Temporary secrets

---

# Limiting Storage

```
emptyDir:
  sizeLimit: 512Mi
```

Example

```
volumes:
- name: shared-volume
  emptyDir:
    sizeLimit: 512Mi
```

This prevents unlimited storage usage.

---

# Your YAML Explained

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod
  namespace: rishi

spec:
  containers:
  - name: nginx-container
    image: nginx

    volumeMounts:
    - name: shared-volume
      mountPath: /usr/share/nginx/html

  volumes:
  - name: shared-volume
    emptyDir:
      sizeLimit: 512Mi
```

Explanation

```
volumeMounts

↓

Mount EmptyDir

↓

/usr/share/nginx/html

↓

Anything written here

↓

Stored inside EmptyDir
```

---

# Verify EmptyDir

Enter Pod

```
kubectl exec -it nginx-pod -n rishi -- sh
```

Create File

```
echo "Hello Kubernetes" > /usr/share/nginx/html/index.html
```

Verify

```
cat /usr/share/nginx/html/index.html
```

Exit

```
exit
```

Open Browser

```
kubectl port-forward pod/nginx-pod 8080:80 -n rishi
```

Visit

```
http://localhost:8080
```

Output

```
Hello Kubernetes
```

---

# Verify Data After Container Restart

Find Container

```
kubectl get pod nginx-pod -n rishi
```

Delete Nginx Process

```
kubectl exec -it nginx-pod -n rishi -- sh
```

```
kill 1
```

Container Restarts Automatically.

Again Check

```
kubectl exec -it nginx-pod -n rishi -- cat /usr/share/nginx/html/index.html
```

Output

```
Hello Kubernetes
```

Data Still Exists ✅

---

# Verify After Pod Delete

Delete Pod

```
kubectl delete pod nginx-pod -n rishi
```

Create Pod Again

```
kubectl apply -f pod.yaml
```

Check

```
kubectl exec -it nginx-pod -n rishi -- ls /usr/share/nginx/html
```

Output

```
Empty
```

Data Deleted ✅

---

# Multi Container Example

```
                 Pod
      ┌─────────────────────────┐
      │                         │
      │     EmptyDir Volume      │
      │                         │
      └───────┬───────────┬─────┘
              │           │
              ▼           ▼
          Nginx       Busybox
         Writes       Reads
```

Example

Container 1

```
echo "Hello" > /shared/index.html
```

Container 2

```
cat /shared/index.html
```

Output

```
Hello
```

---

# Advantages

✔ Very Easy

✔ Fast

✔ Shared Between Containers

✔ No External Storage Needed

✔ Temporary Workspace

✔ Scratch Space

✔ Supports Memory Storage

---

# Disadvantages

❌ Data Lost After Pod Deletion

❌ Not Persistent

❌ Cannot Share Across Pods

❌ Node Failure May Lose Data

---

# EmptyDir vs HostPath vs PVC

| Feature | EmptyDir | HostPath | PVC |
|----------|----------|----------|-----|
| Persistent | ❌ | ✅ | ✅ |
| Shared Between Pods | ❌ | Limited | ✅ |
| Lifetime | Pod | Node | Independent |
| Storage | Node | Node | Storage Class |
| Production | Temporary | Rare | Recommended |

---

# EmptyDir vs ConfigMap

| EmptyDir | ConfigMap |
|-----------|-----------|
| Read Write | Mostly Read Only |
| Temporary | Configuration |
| Stores Data | Stores Config |

---

# EmptyDir vs Secret

| EmptyDir | Secret |
|-----------|--------|
| General Data | Sensitive Data |
| Read Write | Read Only |
| Temporary | Secure |

---

# Real World Use Cases

### Cache

```
Application Cache
```

---

### Log Sharing

Application writes logs

↓

Fluentd reads logs

↓

Send to Elasticsearch

---

### Nginx + Sidecar

Application

↓

Generate HTML

↓

Store in EmptyDir

↓

Nginx Serves HTML

---

### Build Pipeline

Container 1

Compile Code

↓

EmptyDir

↓

Container 2

Package

↓

Container 3

Upload

---

### ML Processing

Download Dataset

↓

Process

↓

Store Temporary Files

↓

Delete Pod

---

### Video Processing

Input Video

↓

FFmpeg

↓

Temporary Frames

↓

Merge

↓

Delete

---

# Best Practices

✔ Use only temporary data

✔ Always define sizeLimit

✔ Use medium: Memory only when needed

✔ Never store database files

✔ Never use for backups

✔ Never use for critical data

---

# Common Interview Questions

### Q1. What is EmptyDir?

Temporary storage created when a Pod starts and deleted when the Pod is deleted.

---

### Q2. Is EmptyDir Persistent?

No.

---

### Q3. Can multiple containers use it?

Yes.

---

### Q4. Can multiple Pods share EmptyDir?

No.

---

### Q5. Where is EmptyDir stored?

Node disk by default or RAM if `medium: Memory` is configured.

---

### Q6. What happens if a container crashes?

Data remains because the Pod still exists.

---

### Q7. What happens if the Pod is deleted?

Entire EmptyDir is deleted.

---

### Q8. Can EmptyDir be memory based?

Yes.

```
emptyDir:
  medium: Memory
```

---

### Q9. Why use sizeLimit?

To prevent a Pod from consuming all available node storage.

---

### Q10. Difference between EmptyDir and PVC?

EmptyDir is temporary and tied to the Pod lifecycle, whereas PVC provides persistent storage independent of the Pod.

---

# Commands

Create Resources

```
kubectl apply -f emptydir.yaml
```

View Pod

```
kubectl get pods -n rishi
```

Describe Pod

```
kubectl describe pod nginx-pod -n rishi
```

Open Shell

```
kubectl exec -it nginx-pod -n rishi -- sh
```

Create File

```
echo "Hello Kubernetes" > /usr/share/nginx/html/index.html
```

Read File

```
cat /usr/share/nginx/html/index.html
```

Port Forward

```
kubectl port-forward pod/nginx-pod 8080:80 -n rishi
```

Delete Pod

```
kubectl delete pod nginx-pod -n rishi
```

---

# Key Takeaways

- EmptyDir is temporary storage tied to a Pod.
- Created automatically when the Pod starts.
- Deleted permanently when the Pod is deleted.
- Supports sharing data between containers in the same Pod.
- Supports disk-based and memory-based storage.
- Ideal for caches, logs, scratch space, and intermediate files.
- Not suitable for persistent or critical application data.

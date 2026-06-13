# Kubernetes Multi-Container Pod Deployment (Nginx + Firefox)

## Overview

This project demonstrates how to create and run a **Multi-Container Pod** in Kubernetes.

A single Pod contains two containers:

1. **Nginx Container**
   - Runs a web server
   - Uses `nginx:latest` Docker image
   - Listens on port `80`

2. **Firefox Container**
   - Runs a browser-based Firefox application
   - Uses `jlesage/firefox:latest` Docker image
   - Listens on port `5800`

This setup helps understand how multiple containers inside the same Kubernetes Pod communicate with each other.

---

# Architecture

```text
                 Kubernetes Cluster

                         |
                         |
                       POD

        +--------------------------------+

          nginx-container

          Image: nginx:latest
          Port : 80

          http://localhost:80

        ----------------------------------

          firefox-container

          Image: jlesage/firefox:latest
          Port : 5800

        +--------------------------------+


Both containers share:

- Same Pod
- Same Network Namespace
- Same IP Address
- Localhost Communication
```

---

# Kubernetes Manifest

File Name:

```bash
pod.yaml
```

Configuration:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod
  labels:
    app: nginx

spec:
  containers:

  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80
    resources:
      requests:
        cpu: "100m"
        memory: "128Mi"
      limits:
        cpu: "2"
        memory: "512Mi"

  - name: firefox-container
    image: jlesage/firefox:latest
    ports:
    - containerPort: 5800
    resources:
      requests:
        cpu: "100m"
        memory: "128Mi"
      limits:
        cpu: "2"
        memory: "512Mi"
```

---

# YAML Explanation

## apiVersion

```yaml
apiVersion: v1
```

Defines the Kubernetes API version.

`v1` is used for creating Pod resources.

---

## kind

```yaml
kind: Pod
```

Defines the Kubernetes object type.

Here we are creating a Pod.

A Pod is the smallest deployable unit in Kubernetes.

---

## metadata

```yaml
metadata:
  name: nginx-pod
```

Defines information about the Pod.

Pod Name:

```text
nginx-pod
```

---

## Labels

```yaml
labels:
  app: nginx
```

Labels are used to identify and organize Kubernetes resources.

Example:

```text
app=nginx
```

---

# Containers

This Pod contains two containers.

---

# Container 1: Nginx

```yaml
name: nginx-container
image: nginx:latest
```

Container name:

```text
nginx-container
```

Docker Image:

```text
nginx:latest
```

Purpose:

Runs Nginx Web Server.

---

## Nginx Port

```yaml
containerPort: 80
```

Nginx listens on HTTP port 80.

Access:

```text
http://localhost:80
```

---

# Container 2: Firefox

```yaml
name: firefox-container
image: jlesage/firefox:latest
```

Container name:

```text
firefox-container
```

Purpose:

Runs Firefox browser inside container.

---

## Firefox Port

```yaml
containerPort: 5800
```

Firefox web interface runs on:

```text
5800
```

---

# Resource Management

Kubernetes allows defining CPU and Memory allocation.

## Requests

```yaml
requests:
  cpu: "100m"
  memory: "128Mi"
```

Minimum guaranteed resources:

CPU:

```text
100m = 0.1 CPU Core
```

Memory:

```text
128Mi RAM
```

---

## Limits

```yaml
limits:
  cpu: "2"
  memory: "512Mi"
```

Maximum allowed resources:

CPU:

```text
2 CPU Cores
```

Memory:

```text
512Mi RAM
```

---

# Deploy Pod

Apply the YAML:

```bash
kubectl apply -f pod.yaml
```

Expected Output:

```text
pod/nginx-pod created
```

---

# Verify Pod Status

```bash
kubectl get pods
```

Example:

```text
NAME          READY     STATUS
nginx-pod     2/2       Running
```

Explanation:

```text
2/2 = Two containers are running successfully
```

---

# Get Detailed Pod Information

```bash
kubectl describe pod nginx-pod
```

Shows:

- Container Status
- Events
- Image Details
- IP Address
- Resource Information

---

# List Containers Inside Pod

```bash
kubectl get pod nginx-pod -o jsonpath='{.spec.containers[*].name}'
```

Output:

```text
nginx-container firefox-container
```

---

# Access Containers

## Login into Nginx Container

```bash
kubectl exec -it nginx-pod \
-c nginx-container -- bash
```

---

## Login into Firefox Container

```bash
kubectl exec -it nginx-pod \
-c firefox-container -- sh
```

---

# Container Communication Test

Containers inside the same Pod communicate using localhost.

From Firefox container:

```bash
curl localhost:80
```

Expected Output:

```html
Welcome to nginx!
```

This confirms Firefox container can communicate with Nginx container.

---

# Check Logs

## Nginx Logs

```bash
kubectl logs nginx-pod \
-c nginx-container
```

## Firefox Logs

```bash
kubectl logs nginx-pod \
-c firefox-container
```

---

# Delete Pod

```bash
kubectl delete pod nginx-pod
```

---

# Important Kubernetes Concept

Containers inside the same Pod:

✔ Share same Network Namespace  
✔ Share same IP Address  
✔ Communicate using localhost  
✔ Run independently  
✔ Have separate filesystems  

Communication Flow:

```text

firefox-container

        |
        |
        localhost:80

        |
        |

nginx-container

```

---

# Interview Questions

### Q1. Can two containers inside the same Pod communicate?

Answer:

Yes.

Containers inside the same Pod share the same network namespace, so they communicate using localhost.

---

### Q2. Do containers inside same Pod have same IP?

Answer:

Yes.

A Pod gets one IP address and all containers share it.

---

### Q3. Why use Multi-Container Pods?

Answer:

When containers need to work closely together.

Examples:

- Sidecar Containers
- Log Collectors
- Proxy Containers
- Helper Containers

---

# Summary

Implemented:

- Kubernetes Pod
- Multi Container Architecture
- Nginx Container
- Firefox Container
- Container Networking
- Resource Requests
- Resource Limits
- Logs Checking
- Troubleshooting Commands

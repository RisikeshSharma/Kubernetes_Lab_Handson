# Kubernetes Lab Handson 🚀

## Overview

This repository contains my hands-on Kubernetes learning journey with practical YAML manifests, real-world scenarios, troubleshooting commands, and Kubernetes core concepts.

The goal of this repository is to understand Kubernetes from basic to production-level concepts by creating and managing different Kubernetes resources.

This repository covers:

- Kubernetes Architecture
- Pods
- Multi-Container Pods
- Deployments
- ReplicaSets
- Services
- ConfigMaps
- Secrets
- Namespaces
- Resource Management
- Health Checks
- Storage
- Scheduling
- Troubleshooting
- Kubernetes Commands

---

# What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform used to automate:

- Container Deployment
- Container Scaling
- Container Networking
- Container Recovery
- Container Management

It helps manage containerized applications across multiple servers.

---

# Kubernetes Architecture

```text

                 User / DevOps Engineer

                         |
                         |

                    kubectl CLI

                         |
                         |

                Kubernetes API Server


================================================

                CONTROL PLANE

================================================


API Server
     |
     |
Scheduler
     |
     |
Controller Manager
     |
     |
ETCD Database


================================================

                WORKER NODE

================================================


Kubelet

Container Runtime
(Docker/containerd)

Pods

Containers


```

---

# Kubernetes Components

## Control Plane Components

### API Server

Entry point of Kubernetes Cluster.

Responsibilities:

- Accept requests
- Validate requests
- Communicate with cluster components


---

## ETCD

ETCD is Kubernetes database.

Stores:

- Cluster configuration
- Current state
- Metadata


---

## Scheduler

Scheduler decides where Pods should run.

It checks:

- CPU availability
- Memory availability
- Node health
- Scheduling rules


---

## Controller Manager

Maintains desired state of Kubernetes.

Example:

Desired:

```
3 Pods
```

Actual:

```
2 Pods
```

Controller creates one more Pod.

---

# Worker Node Components


## Kubelet

Agent running on every worker node.

Responsibilities:

- Communicates with API Server
- Creates Pods
- Monitors containers


---

## Container Runtime

Runs containers.

Examples:

- containerd
- Docker


---

# Kubernetes Resources Covered


## 1. Pod

Smallest deployable unit in Kubernetes.

A Pod contains one or more containers.

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
  - name: nginx
    image: nginx
```

Commands:

```bash
kubectl apply -f pod.yaml

kubectl get pods

kubectl describe pod nginx-pod
```

---

# 2. Multi Container Pod

Multiple containers running inside same Pod.

Features:

- Same IP Address
- Same Network Namespace
- localhost communication


Example:

```text

POD

+----------------+

nginx-container

firefox-container

+----------------+

```

Communication:

```bash
localhost:80
```

---

# 3. ReplicaSet

ReplicaSet maintains number of Pod replicas.

Example:

Required:

```
3 Pods
```

If one Pod fails:

```
ReplicaSet creates new Pod automatically
```

Commands:

```bash
kubectl get rs
```

---

# 4. Deployment

Deployment manages application releases.

Provides:

- Rolling Updates
- Rollbacks
- Scaling


Commands:

Create:

```bash
kubectl apply -f deployment.yaml
```

Scale:

```bash
kubectl scale deployment nginx --replicas=5
```

Rollback:

```bash
kubectl rollout undo deployment nginx
```

---

# 5. Kubernetes Services

Service exposes applications.

Types:

## ClusterIP

Internal communication.

## NodePort

Expose application outside cluster.

## LoadBalancer

Cloud load balancer integration.


Commands:

```bash
kubectl get svc
```

---

# 6. ConfigMap

Stores non-sensitive configuration.

Examples:

- URLs
- Environment variables
- Application settings


Command:

```bash
kubectl get configmap
```

---

# 7. Secrets

Stores sensitive information.

Examples:

- Passwords
- Tokens
- Certificates


Command:

```bash
kubectl get secrets
```

---

# 8. Namespace

Used for resource isolation.

Create:

```bash
kubectl create namespace dev
```

Check:

```bash
kubectl get namespaces
```

---

# 9. Resource Management


Requests:

Minimum required resources.

Example:

```yaml
requests:
 cpu: 100m
 memory: 128Mi
```


Limits:

Maximum allowed resources.

Example:

```yaml
limits:
 cpu: 1
 memory: 512Mi
```

---

# 10. Health Checks


## Liveness Probe

Checks application is alive.

If failed:

Kubernetes restarts container.


## Readiness Probe

Checks application is ready for traffic.


---

# 11. Kubernetes Storage


## Persistent Volume (PV)

Cluster storage resource.


## Persistent Volume Claim (PVC)

Storage request from application.


---

# Important Kubectl Commands


Cluster:

```bash
kubectl cluster-info
```

Nodes:

```bash
kubectl get nodes
```

Pods:

```bash
kubectl get pods -A
```

Describe:

```bash
kubectl describe pod <pod-name>
```

Logs:

```bash
kubectl logs <pod-name>
```

Exec:

```bash
kubectl exec -it <pod-name> -- bash
```

Delete:

```bash
kubectl delete -f file.yaml
```

---

# Troubleshooting Commands


Check Events:

```bash
kubectl get events
```


Check Pod Issue:

```bash
kubectl describe pod pod-name
```


Check Logs:

```bash
kubectl logs pod-name
```


Check Node:

```bash
kubectl describe node node-name
```

---

# Common Pod Issues

## ImagePullBackOff

Reason:

- Wrong image name
- Authentication issue


## CrashLoopBackOff

Reason:

- Application crashing
- Wrong configuration


## Pending State

Reason:

- Resource unavailable
- Scheduling issue


---

# Skills Practiced

✔ Kubernetes Architecture  
✔ YAML Manifest Creation  
✔ Container Deployment  
✔ Application Scaling  
✔ Networking  
✔ Storage Management  
✔ Resource Limits  
✔ Troubleshooting  
✔ Real Production Commands  


---

# Author

Created as part of my Kubernetes DevOps Hands-on Practice.

Goal:

Build strong Kubernetes troubleshooting and production-level skills.

# Kubernetes Network Policy

> A complete beginner-to-advanced guide to understanding, implementing, testing, and troubleshooting Kubernetes Network Policies.

---

# Table of Contents

1. Introduction
2. What is NetworkPolicy?
3. Why NetworkPolicy?
4. How NetworkPolicy Works
5. Prerequisites
6. Supported CNI Plugins
7. Important Components
8. Lab Architecture
9. Folder Structure
10. Create Resources
11. Test Connectivity
12. Network Policy Scenarios
13. Verification Commands
14. Troubleshooting
15. Best Practices
16. Interview Questions
17. Cleanup
18. Quick Revision

---

# 1. Introduction

A Kubernetes NetworkPolicy controls how Pods communicate with each other and with external networks.

It works like a firewall for Pods by allowing or denying network traffic.

Without a NetworkPolicy, Kubernetes allows all traffic between Pods by default.

---

# 2. What is NetworkPolicy?

A NetworkPolicy controls communication between:

- Pod → Pod
- Pod → Namespace
- Pod → External Network (CIDR)
- Pod → Internet

It works at **OSI Layer 3 (IP)** and **Layer 4 (TCP/UDP)**.

It **cannot** filter:

- URLs
- HTTP Methods
- HTTP Headers
- Application Data

---

# 3. Why NetworkPolicy?

Benefits

- Improve Security
- Zero Trust Networking
- Micro Segmentation
- Restrict East-West Traffic
- Compliance
- Least Privilege Access
- Protect Sensitive Applications

---

# 4. How NetworkPolicy Works

NetworkPolicy is enforced by the Kubernetes CNI Plugin.

A NetworkPolicy only affects Pods selected by its **podSelector**.

Pods that are **not selected** continue using Kubernetes default behavior (Allow All).

If multiple NetworkPolicies select the same Pod, they are **additive**.

There is **no priority order**.

---

# 5. Prerequisites

- Kubernetes Cluster
- kubectl Installed
- Supported CNI Plugin
- Namespace
- Pods
- Basic Kubernetes Knowledge

---

# 6. Supported CNI Plugins

Supported

- Calico
- Cilium
- Azure CNI Powered by Cilium
- Antrea

Not Supported

- kubenet
- Flannel (Default)

---

# 7. Important Components

## podSelector

Selects Pods.

Example

```yaml
podSelector:
  matchLabels:
    app: nginx
```

---

## namespaceSelector

Selects Namespaces.

---

## ipBlock

Allows or Denies CIDR ranges.

Example

```yaml
ipBlock:
  cidr: 0.0.0.0/0
```

---

## policyTypes

Two Types

- Ingress
- Egress

---

## Ingress

Controls Incoming Traffic.

---

## Egress

Controls Outgoing Traffic.

---

# 8. Lab Architecture

```
Namespace (rishi)
        │
        ▼
Firefox Pod (Client)
        │
        ▼
Nginx Pod (Server)
        │
        ▼
Connectivity Test
        │
        ▼
Apply NetworkPolicy
        │
        ▼
Retest Connectivity
        │
        ▼
Verify Results
```

---

# 9. Folder Structure

```text
Network_Policy/
│
├── Namespace.yaml
├── NginxPod.yaml
├── FirefoxPod.yaml
├── NetworkPolicy.yaml
└── README.md
```

---

# 10. Create Resources

Create Namespace

```bash
kubectl apply -f Namespace.yaml
```

Create Pods

```bash
kubectl apply -f NginxPod.yaml
kubectl apply -f FirefoxPod.yaml
```

Verify

```bash
kubectl get pods -n rishi -o wide
```

---

# 11. Test Connectivity

Find Pod IP

```bash
kubectl get pods -o wide -n rishi
```

Open Firefox Pod

```bash
kubectl exec -it firefox-pod -n rishi -- sh
```

Test Nginx

```bash
curl http://<nginx-pod-ip>
```

Expected

✔ Nginx Welcome Page

---

# 12. Network Policy Scenarios

---

## Scenario 1

### No NetworkPolicy

Expected

✔ All Traffic Allowed

---

## Scenario 2

### Default Deny Ingress

```yaml
policyTypes:
- Ingress
```

Expected

❌ Incoming Traffic Blocked

✔ Outgoing Traffic Allowed

---

## Scenario 3

### Default Deny Egress

```yaml
policyTypes:
- Egress
```

Expected

✔ Incoming Traffic Allowed

❌ Outgoing Traffic Blocked

---

## Scenario 4

### Default Deny Ingress + Egress

```yaml
policyTypes:
- Ingress
- Egress
```

Expected

❌ Incoming Blocked

❌ Outgoing Blocked

---

## Scenario 5

### Allow TCP Port 80

```yaml
ingress:
- ports:
  - protocol: TCP
    port: 80

egress:
- ports:
  - protocol: TCP
    port: 80
```

Expected

✔ Only TCP Port 80 Allowed

---

## Scenario 6

### Allow Firefox Pod

```yaml
ingress:
- from:
  - podSelector:
      matchLabels:
        app: firefox
```

Expected

Firefox → Nginx ✔

Others ✖

---

## Scenario 7

### Allow Namespace

Use namespaceSelector.

---

## Scenario 8

### Allow Internet

```yaml
ipBlock:
  cidr: 0.0.0.0/0
```

---

## Scenario 9

### Allow DNS

Allow UDP/TCP Port 53.

---

## Scenario 10

### Multiple Policies

NetworkPolicies are additive.

No priority exists.

---

# 13. Verification Commands

```bash
kubectl get networkpolicy -A

kubectl describe networkpolicy -n rishi

kubectl get pods -o wide -n rishi

kubectl get svc -n rishi

kubectl exec -it firefox-pod -n rishi -- curl http://<nginx-pod-ip>

kubectl get events -n rishi

kubectl logs firefox-pod -n rishi
```

---

# 14. Troubleshooting

## Pod Pending

- Insufficient Resources
- Node Not Ready

---

## CrashLoopBackOff

- Application Crash
- Invalid Configuration

---

## OOMKilled

Increase Memory Limit.

---

## Connection Refused

Application not listening.

---

## Connection Timeout

Possible Causes

- NetworkPolicy
- DNS Failure
- Service Issue
- Wrong Pod IP
- Wrong Labels

---

## NetworkPolicy Not Working

Check

- Supported CNI
- Pod Labels
- Namespace
- policyTypes
- Pod Selector
- Events
- Describe NetworkPolicy

---

# 15. Best Practices

- Default Deny First
- Follow Least Privilege Principle
- Use Labels Carefully
- Prefer Services Instead of Pod IP
- Separate Namespaces
- Test Every Policy
- Keep Policies Simple
- Version Control Policies
- Document Every Policy

---

# 16. Interview Questions

### What is NetworkPolicy?

Firewall for Pods.

---

### Does NetworkPolicy work without supported CNI?

No.

---

### Is NetworkPolicy Stateful?

Yes.

Reply traffic is automatically allowed.

---

### What is Ingress?

Incoming Traffic.

---

### What is Egress?

Outgoing Traffic.

---

### Can multiple NetworkPolicies exist?

Yes.

Policies are additive.

---

### Can NetworkPolicy block Services?

No.

It applies to Pods selected by podSelector.

---

### Can NetworkPolicy filter URLs?

No.

Only Layer 3 and Layer 4.

---

### What is ipBlock?

CIDR-based rule.

---

### Why does DNS stop working?

Because Egress blocks TCP/UDP Port 53.

---

### Which CNIs support NetworkPolicy?

- Calico
- Cilium
- Azure CNI Powered by Cilium
- Antrea

---

### Production Recommendation

✔ Default Deny

✔ Least Privilege

✔ Separate Namespaces

✔ Audit Policies

✔ Version Control

---

# 17. Cleanup

```bash
kubectl delete -f NetworkPolicy.yaml

kubectl delete -f FirefoxPod.yaml

kubectl delete -f NginxPod.yaml

kubectl delete -f Namespace.yaml
```

---

# 18. Quick Revision

```
NetworkPolicy = Pod Firewall

Ingress = Incoming Traffic

Egress = Outgoing Traffic

Default Kubernetes = Allow All

Default Deny = Recommended

Needs Supported CNI

Policies are Additive

Stateful

Layer 3 & Layer 4

Zero Trust Networking

Least Privilege
```

---

# Author

**Rishikesh Sharma**

Happy Learning 🚀
# Kubernetes Network Policy 

> A complete guide to understand, implement and troubleshoot Kubernetes Network Policies.

## Table of Contents
1. What is Network Policy?
2. Why Network Policy?
3. How it Works
4. Prerequisites
5. Components
6. Lab Architecture
7. Folder Structure
8. Create Namespace
9. Create Pods
10. Test Connectivity
11. Network Policy Scenarios
12. Commands
13. Troubleshooting
14. Interview Questions
15. Best Practices

# 1. What is Network Policy?

A Kubernetes NetworkPolicy controls how Pods communicate with:
- Other Pods
- Namespaces
- External IPs (CIDR)

It acts like a firewall for Pods.

> By default Kubernetes allows all traffic unless a NetworkPolicy selects the Pod.

---

# 2. Why Network Policy?

- Secure workloads
- Zero Trust Networking
- Micro Segmentation
- Restrict east-west traffic
- Compliance

---

# 3. How Network Policy Works

NetworkPolicy is enforced by the CNI plugin.

Supported CNIs:
- Calico
- Cilium
- Azure CNI Powered by Cilium
- Antrea

Not supported:
- kubenet
- Flannel (default)

---

# 4. Important Concepts

## podSelector
Selects destination Pods.

## namespaceSelector
Selects Namespace.

## ipBlock
Allows external CIDR.

## policyTypes

- Ingress
- Egress

---

# 5. Folder Structure

```text
Network_Policy/
├── Namespaces.yaml
├── NginxPod.yaml
├── FirefoxPod.yaml
├── NetworkPolicy.yaml
├── README.md
```

---

# 6. Lab Flow

Namespace

↓

Nginx Pod

↓

Firefox Pod

↓

Connectivity Test

↓

Apply Network Policy

↓

Retest

---

# 7. Commands

Create Namespace

```bash
kubectl apply -f Namespaces.yaml
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

Test Connectivity

```bash
kubectl exec -it firefox-pod -n rishi -- curl http://<nginx-pod-ip>
```

---

# 8. Scenarios

## Scenario 1
No Network Policy

Expected:
✔ All traffic allowed

---

## Scenario 2
Default Deny Ingress

```yaml
policyTypes:
- Ingress
```

Expected:
❌ Incoming blocked

---

## Scenario 3
Allow Firefox Pod

Use podSelector.

Expected

Firefox → Nginx ✔

Others ✖

---

## Scenario 4
Allow Namespace

Use namespaceSelector.

---

## Scenario 5
Allow Port 80 Only

```yaml
ports:
- protocol: TCP
  port: 80
```

---

## Scenario 6
Default Deny Egress

```yaml
policyTypes:
- Egress
```

Expected

No Internet

No DNS

No Pod communication

---

## Scenario 7
Allow DNS

Allow UDP/TCP 53.

---

## Scenario 8
Allow Internet using ipBlock

```yaml
ipBlock:
  cidr: 0.0.0.0/0
```

---

## Scenario 9
Ingress + Egress

Both directions controlled.

---

## Scenario 10
Multiple Policies

Policies are additive.

There is NO priority.

---

# 9. Verification Commands

```bash
kubectl get networkpolicy -A

kubectl describe networkpolicy -n rishi

kubectl exec -it firefox-pod -n rishi -- curl http://<nginx-ip>

kubectl logs firefox-pod -n rishi

kubectl get events -n rishi
```

---

# 10. Troubleshooting

Pending Pod
- Node issue
- Resource issue

CrashLoopBackOff
- App crash

OOMKilled
- Increase memory

Connection Refused
- App not listening

Timeout
- NetworkPolicy
- DNS
- Service

---

# 11. Best Practices

- Default deny first
- Allow only required traffic
- Use labels carefully
- Separate namespaces
- Test every policy
- Keep policies simple

---

# 12. Interview Questions

### What is NetworkPolicy?
Firewall for Pods.

### Does NetworkPolicy work without supported CNI?
No.

### Is Service protected?
No.
Policy applies to Pods.

### Difference between Ingress and Egress?

Ingress = Incoming

Egress = Outgoing

### Can multiple policies exist?

Yes.
They are additive.

### Can NetworkPolicy block NodePort?

No.

### Can it filter HTTP URLs?

No.
Layer 3/4 only.

### What is ipBlock?

CIDR based rule.

### Why DNS stops working?

Because Egress blocks UDP/TCP 53.

### Which CNIs support NetworkPolicy?

Calico

Cilium

Azure CNI Powered by Cilium

### Production Recommendation

✔ Default deny

✔ Least privilege

✔ Separate namespaces

✔ Audit regularly

✔ Version control policies

---

# 13. Cleanup

```bash
kubectl delete -f NetworkPolicy.yaml

kubectl delete -f FirefoxPod.yaml

kubectl delete -f NginxPod.yaml

kubectl delete -f Namespaces.yaml
```

---

# Quick Revision

NetworkPolicy = Firewall

Ingress = Incoming

Egress = Outgoing

Needs Supported CNI

Policies are Additive

Default Kubernetes = Allow All

Default Deny = Recommended

Zero Trust = Best Practice

Happy Learning 🚀

# Resource Requirements in Kubernetes

## Overview

In a Kubernetes cluster with multiple nodes, each node has its own CPU and memory resources. The **kube-scheduler** schedules new pods on nodes by evaluating:
- How much memory and CPU the pod needs
- What resources are available on each node

---

## Defining Resource Requirements

Resource requirements are specified in the pod definition under:
```yaml
spec:
  containers:
  - name: my-app
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

---

## CPU Units

**1 count of CPU equals:**
- 1 vCPU in AWS
- 1 Core in GCP  
- 1 vCore in Azure
- 1 Hyperthread

**CPU can be specified as:**
- `1` = 1 full CPU core
- `0.5` or `500m` = half a CPU core
- `100m` = 0.1 CPU core (10% of a core)

---

## Memory Units

**Memory uses binary units:**
- `1 Ki` (kibibyte) = 1,024 bytes
- `1 Mi` (mebibyte) = 1,024 Ki = 1,048,576 bytes
- `1 Gi` (gibibyte) = 1,024 Mi

**Example memory specifications:**
- `64Mi` = 64 mebibytes
- `1Gi` = 1 gibibyte
- `512Mi` = 512 mebibytes

---

## What Happens When Limits Are Exceeded?

### CPU Limit Exceeded
When a pod tries to exceed its CPU limit, the system **throttles** the CPU so it doesn't go beyond the specified limit.

**Real-world analogy:** 
> Imagine a car that can go 200 km/h, but you install a limiter that caps it at 100 km/h. No matter how hard you press the pedal, it won't go faster than 100 km/h.

**Example scenario:**
- Container is guaranteed `0.5 CPU` (request)
- It can use up to `1 full CPU core` (limit)
- If it tries to use more than 1 core, it will be throttled

**Signs of CPU throttling:**
- App performance may slow down
- Logs may show increased latency
- Metrics tools (like Prometheus) show high CPU throttling time

### Memory Limit Exceeded
**Memory behaves differently than CPU.** A container can temporarily use more memory than its limit, but:

- If a pod **consistently** tries to consume more memory than its limit
- The pod will be **terminated** (OOMKilled - Out of Memory Killed)
- You'll see the pod status as `OOMKilled` or similar termination reason

---

## Best Practices

1. **Set requests** to guarantee minimum resources
2. **Set limits** to prevent resource hogging
3. **Monitor** your applications to tune requests and limits appropriately
4. **Test** in staging environments to find optimal values
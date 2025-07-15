# Taints, Tolerations, and Node Affinity Combined

## Problem Statement

In a shared cluster environment, we want to ensure that:

- **Our dedicated nodes** do not run pods from other teams
- **Our pods** are not placed on nodes that are primarily serving other workloads

---

## Two Approaches to Solve This

### First Approach: Taints and Tolerations

In this method, each node is tainted with its respective color (blue, red, or green). Correspondingly, each pod is given a toleration that matches the node's taint.

**This ensures:**
- Only pods with the appropriate toleration can be scheduled on the tainted node

**Example:** The green pod will only be scheduled on the green node, and the blue pod on the blue node.

**Limitation:** While taints and tolerations can prevent pods lacking the correct toleration from being scheduled on these nodes, they do **not guarantee** that the pods will preferentially use the dedicated nodes. Consequently, there might be cases where the red pod is scheduled on a node without the corresponding taint and toleration, resulting in undesired pod placement.

---

### Second Approach: Node Affinity

Node affinity offers another strategy by allowing you to:

- **Label nodes** with their respective colors (blue, red, and green)
- **Define node affinity rules** or selectors on pods to match these labels

**Benefits:** This method directs pods to the correct nodes based on labels.

**Limitation:** Although node affinity helps achieve the desired pod distribution, it does **not prevent** other pods from being scheduled on the same nodes, which could lead to conflicts with other teams' deployments.

---

## Combining Taints, Tolerations, and Node Affinity

To fully dedicate nodes exclusively for specific pods, you can harness the strengths of both approaches by combining them:

### Step 1: Apply Taints

First, apply taints on your nodes. This ensures that pods without the matching toleration are **not scheduled** on these nodes.

```sh
kubectl taint nodes blue-node color=blue:NoSchedule
kubectl taint nodes red-node color=red:NoSchedule
kubectl taint nodes green-node color=green:NoSchedule
```

### Step 2: Implement Node Affinity

Next, implement node affinity rules on your pods. This guarantees that the pods are scheduled **only on the nodes** with the corresponding color labels.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: blue-pod
spec:
  containers:
    - name: app
      image: nginx
  tolerations:
    - key: "color"
      operator: "Equal"
      value: "blue"
      effect: "NoSchedule"
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: color
                operator: In
                values:
                  - blue
```

---

## Benefits of the Combined Approach

By using both strategies together, you achieve **strict node allocation**:

| Benefit | Description |
|---------|-------------|
| **Exclusive Access** | Only your dedicated pods run on their targeted nodes |
| **No Cross-Team Conflicts** | Prevents unintended pod placements from other teams |
| **Guaranteed Placement** | Pods are scheduled only where they should be |
| **Resource Isolation** | Teams get dedicated compute resources |

---

## Summary

| Approach | Prevents Other Pods | Guarantees Correct Placement |
|----------|:------------------:|:---------------------------:|
| **Taints & Tolerations** | ✅ | ❌ |
| **Node Affinity** | ❌ | ✅ |
| **Combined Approach** | ✅ | ✅ |

**Result:** Perfect node dedication for team-specific workloads in shared clusters.

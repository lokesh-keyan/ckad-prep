# Taints and Tolerations in Kubernetes

## Analogy: Bug Repellent

Imagine a bug approaching a person. To deter the bug, you spray the person with a repellent. Here:
- **Repellent** = Taint applied on a node
- **Person** = Node
- **Bugs** = Pods
- **Resistant bugs** = Pods that can tolerate the taint

Most bugs (pods) are repelled and cannot land on the person (node). However, some bugs might be resistant to the repellent; these represent pods that can tolerate the taint.

---

## Purpose of Taints and Tolerations

In Kubernetes, taints are applied to nodes solely to **restrict which pods can be scheduled** on them. They do **not** serve as a security measure.

---

## Example Scenario

Consider a simple cluster with:
- **3 worker nodes**: one, two, and three
- **4 pods**: A, B, C, and D

Without any restrictions, the Kubernetes scheduler evenly distributes these pods among the nodes.

### Goal: Dedicate Node One for Specific Application

1. **Apply a taint** to node one (e.g., key "app" and value "blue")
2. Since pods do not come with tolerations by default, they will be excluded from node one
3. **Add a toleration** to specific pods (e.g., pod D) to allow them on node one

With taints and tolerations configured appropriately:
- Scheduler avoids node one for pods A, B, and C
- Only pod D can run on node one

---

## Applying Taints to Nodes

### Command Syntax
```sh
kubectl taint nodes node-name key=value:taint-effect
```

### Example
```sh
kubectl taint nodes node1 app=blue:NoSchedule
```

---

## Taint Effects

The taint effect determines the behavior for pods that do not tolerate the taint:

| Effect | Description |
|--------|-------------|
| **NoSchedule** | Pods without the required toleration are not scheduled on the node |
| **PreferNoSchedule** | The scheduler avoids placing a pod on the node if possible, but it is not strictly enforced |
| **NoExecute** | New pods that do not tolerate the taint are not scheduled, and existing pods without the toleration are evicted |

---

## Adding Tolerations to Pods

To add a toleration to a pod, update its pod definition file by including a `tolerations` section under `spec`.

### Example Pod with Toleration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  tolerations:
    - key: "app"
      operator: "Equal"
      value: "blue"
      effect: "NoSchedule"
```

> **Important:** Ensure that each value is correctly enclosed in double quotes as shown.

---

## NoExecute Effect in Detail

### Scenario
1. **Initial state**: Three nodes running workloads without any taints
2. **Later**: Node one is dedicated to a special application
3. **Action**: Taint node one using the `NoExecute` effect
4. **Result**: Add toleration only to the designated pod (pod D)

### Outcome
- **NoExecute taint** ensures that any pod on node one that does not tolerate the taint (e.g., pod C) is **evicted**
- **Pod D remains** since it has the necessary toleration

---

## Key Points

- Taints and tolerations work together to control pod placement
- Taints repel pods from nodes
- Tolerations allow pods to be scheduled on tainted nodes
- Use taints to dedicate nodes for specific workloads
- NoExecute effect can evict existing pods that don't tolerate the taint


Kubernetes matches tolerations and taints strictly by effect.

If a pod has a toleration with the wrong effect, it won’t work.

You can add multiple tolerations to a pod to handle multiple taints.

## To remove a taint
kubectl taint nodes node01 env:NoSchedule-
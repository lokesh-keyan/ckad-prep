# Node Affinity in Kubernetes

Node affinity provides more After defining affinity rules, the Kubernetes scheduler evaluates these rules when placing Pods on nodes. Here's how different affinity types work:

### 1. Required During Scheduling, Ignored During Execution

- **During Scheduling**: The Pod must run on a node that meets the affinity rules; if there's no match, the Pod is not scheduled
- **During Execution**: Once running, changes to node labels do not affect the Pod

### 2. Preferred During Scheduling, Ignored During Execution

- **During Scheduling**: The scheduler prefers nodes that meet the affinity rules but will schedule the Pod on any available node if no perfect match exists
- **During Execution**: Similar to the required type, changes to node labels do not impact the running Pod

---

## Node Affinity vs Node Selectors

| Feature | Node Selectors | Node Affinity |
|---------|----------------|---------------|
| **Syntax** | Simple key-value pairs | More complex match expressions |
| **Operators** | Equality only | `In`, `NotIn`, `Exists`, `DoesNotExist` |
| **Logic** | AND only | Supports OR and AND logic |
| **Flexibility** | Limited | High flexibility for complex requirements |
| **Use Case** | Simple node selection | Complex scheduling requirements |

---

## Best Practices

1. **Use node selectors** for simple, straightforward node selection requirements
2. **Use node affinity** when you need complex logic or multiple conditions
3. **Label your nodes** appropriately before applying affinity rules
4. **Test scheduling** behavior in development environments before production deploymenty than node selectors for scheduling pods on specific nodes. The following YAML configuration demonstrates how to specify node affinity:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: data-processor
      image: data-processor
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Large
```

## Configuration Explanation

- **`affinity`**: Under `spec`, the affinity field is introduced with a child field named `nodeAffinity`
- **`requiredDuringSchedulingIgnoredDuringExecution`**: Ensures that the scheduler places the Pod on a node that complies with the specified rules. Once the Pod runs, modifications to node labels are ignored
- **`nodeSelectorTerms`**: An array where you define one or more match expressions. Each match expression includes:
  - **Key**: e.g., "size"
  - **Operator**: e.g., `In` - specifies how the label's value is evaluated using the provided values list
  - **Values**: In this case, the Pod will be placed on nodes where the label "size" is set to "Large"

---

## Excluding Nodes Using Node Affinity
To exclude nodes with a specific label value, you can use the `NotIn` operator:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: data-processor
      image: data-processor
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: NotIn
                values:
                  - Small
```

---

## Node Affinity Types
After defining affinity rules, the Kubernetes scheduler evaluates these rules when placing Pods on nodes. Here’s how different affinity types work:

Required During Scheduling, Ignored During Execution:

During Scheduling: The Pod must run on a node that meets the affinity rules; if there's no match, the Pod is not scheduled.
During Execution: Once running, changes to node labels do not affect the Pod.
Preferred During Scheduling, Ignored During Execution:

During Scheduling: The scheduler prefers nodes that meet the affinity rules but will schedule the Pod on any available node if no perfect match exists.
During Execution: Similar to the required type, changes to node labels do not impact the running Pod.


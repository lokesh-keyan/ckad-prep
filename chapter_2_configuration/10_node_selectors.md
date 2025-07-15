# Node Selectors in Kubernetes

## Scenario

Imagine you have a three-node cluster where:
- **Two nodes** are smaller with limited hardware resources
- **One node** is larger with higher resources

Your cluster runs various workloads, but you want to ensure that resource-intensive data processing pods always run on the larger node. By default, Kubernetes can schedule any pod on any available node, which might result in a resource-heavy pod running on a smaller node.

## Solution: Node Selectors

To overcome this, you can restrict pods to run on specific nodes by using **node selectors**. A node selector allows you to define a key-value pair in your pod definition that corresponds to labels assigned to nodes.

---

## Pod Definition Using Node Selectors

Below is an example of a pod definition in YAML format that uses a node selector to ensure the pod runs exclusively on the larger node:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: data-processor
      image: data-processor
  nodeSelector:
    size: Large
```

In this example:
- The `nodeSelector` property is added under the `spec` section
- It specifies the key-value pair `size: Large`
- These labels must be assigned to the corresponding nodes

---

## Labeling Your Nodes

Before creating a pod with a node selector, ensure your nodes are labeled appropriately.

### Label a Node Command

```sh
kubectl label nodes <node-name> <label-key>=<label-value>
```

### Example

```sh
kubectl label nodes node-1 size=Large
```

After labeling the node, create the pod with the node selector. When the pod is created, it will be scheduled on the node labeled `size=Large` (in this example, `node-1`).

### Create the Pod

```sh
kubectl create -f pod-definition.yaml
```

---

## Limitations of Node Selectors

While node selectors are effective for basic node restrictions, they have limitations when handling more complex scheduling requirements.

### Example Limitation

If you need to schedule a pod on a node labeled either "Large" **OR** "Medium" while excluding nodes labeled "Small," node selectors cannot express this conditional logic.

### Solution for Complex Requirements

For complex scheduling requirements, use **Node Affinity** instead, which provides:
- Multiple operators (`In`, `NotIn`, `Exists`, `DoesNotExist`)
- Logical operations (AND/OR)
- Preferred vs Required scheduling rules

---

## Summary

| Feature | Node Selectors | Use Case |
|---------|----------------|----------|
| **Syntax** | Simple key-value pairs | Basic node selection |
| **Logic** | Equality matching only | Single condition matching |
| **Complexity** | Low | Simple scheduling requirements |
| **Flexibility** | Limited | Straightforward node restrictions |

**Next Step:** Learn about Node Affinity for more advanced scheduling capabilities.
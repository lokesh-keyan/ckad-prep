## Understanding Kubernetes Namespaces

Imagine there are two boys named **Mark Smith** and **Mark Williams**. Inside their house, they are both called **Mark**, but outside, they are referred to by their **full names**. This analogy helps understand how **namespaces** work in Kubernetes.

In Kubernetes:

- A **namespace** is like a house.
- Inside the same namespace, resources (like pods or services) can have the same name.
- Outside the namespace, they are referred to by their full names (including the namespace) to avoid conflicts.

### Default and System Namespaces

Kubernetes comes with a few built-in namespaces:

- **default**: If you don’t specify a namespace, Kubernetes assigns resources here by default.
- **kube-system**: Contains system-related resources used by Kubernetes itself.
- **kube-public**: Intended for resources that need to be publicly shared or visible to all users.

### Custom Namespaces for Environments

You can create custom namespaces such as `dev` and `prod` to separate development and production environments. Each namespace can have its own:

- Resource quotas
- Network policies
- Access controls

### Cross-Namespace Communication

To access a service from one namespace in another, you use the full DNS path. For example:

```python
mysql.connect("db-service-dev.svc.cluster.local")
```

This follows the structure:

```
<service-name>.<namespace>.svc.cluster.local
```

Kubernetes automatically manages the DNS resolution for these service names within the cluster.

---

Namespaces help organize, isolate, and manage resources efficiently within a Kubernetes cluster.

Here is a sample namespace YAML manifest:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

You can apply this using:

```bash
kubectl apply -f namespace-dev.yaml
```

Alternatively, you can create the namespace directly with kubectl:

```bash
kubectl create namespace dev
```

To set the current context to use a specific namespace by default, use the following command:

```bash
kubectl config set-context --current --namespace=dev
```

This eliminates the need to specify the `--namespace` flag in every command.
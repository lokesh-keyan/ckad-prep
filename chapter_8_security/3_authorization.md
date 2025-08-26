# Authorization

In previous lessons, we explored authentication and demonstrated how users or processes gain access to a cluster. With access granted, the next important question is: what actions can they perform? This is where authorization comes into play.

As a cluster administrator, you hold full control over operations such as viewing cluster objects (e.g., Pods, Nodes, and Deployments) and modifying them. For example:

```bash
kubectl get pods
```

```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          53s
```

```bash
kubectl get nodes
```

```
NAME        STATUS   ROLES    AGE     VERSION
worker-1    Ready    <none>   5d21h   v1.13.0
worker-2    Ready    <none>   5d21h   v1.13.0
```

```bash
kubectl delete node worker-2
```

```
Node worker-2 Deleted!
```

---

## Restricted User Access

Consider the following interactions for a user with restricted privileges:

```bash
kubectl get pods
```

```
Error from server (Forbidden): pods is forbidden: User "Bot-1" cannot list "pods"
```

```bash
kubectl get nodes
```

```
Error from server (Forbidden): nodes is forbidden: User "Bot-1" cannot get "nodes"
```

```bash
kubectl delete node worker-2
```

```
Error from server (Forbidden): nodes "worker-2" is forbidden: User "developer" cannot
```

---

## Authorization Mechanisms


Authorization in Kubernetes is implemented through multiple mechanisms, including **Node authorization**, **Attribute-Based Access Control (ABAC)**, **Role-Based Access Control (RBAC)**, and **Webhook modes**. Each mechanism plays a distinct role in managing access control:

### Node Authorization

The Kubernetes API server is not only accessed by administrators but also by kubelets (nodes) that report the status of nodes and pods. Kubelets use a special node authorizer to handle requests from users whose names are prefixed by `"system:node"` and who belong to the `"system:nodes"` group. This node authorizer validates each request based on naming conventions and group memberships, ensuring that node-level actions are properly verified.

### Attribute-Based Access Control (ABAC)

For external API access, ABAC lets you tie individual users or groups to a set of permissions. For example, you can define a policy in a JSON file to allow a developer to view, create, and delete pods:

```json
{"kind": "Policy", "spec": {"user": "dev-user", "namespace": "*", "resource": "pods", "apiGroup": "*"}}
{"kind": "Policy", "spec": {"user": "dev-user-2", "namespace": "*", "resource": "pods", "apiGroup": "*"}}
{"kind": "Policy", "spec": {"group": "dev-users", "namespace": "*", "resource": "pods", "apiGroup": "*"}}
{"kind": "Policy", "spec": {"user": "security-1", "namespace": "*", "resource": "csr", "apiGroup": "*"}}
```

However, changes to a user's permissions require editing the policy file and restarting the Kubernetes API server, making ABAC somewhat cumbersome. This is why many organizations prefer Role-Based Access Control (RBAC).

---

## Authorization Flow

When multiple authorization modes are enabled, each incoming request is processed sequentially in the specified order. For example, the request is first evaluated by the Node authorizer; if the request is either not applicable or denied, it then passes to the next module (such as RBAC). As soon as one module approves the request, the evaluation stops and access is granted.


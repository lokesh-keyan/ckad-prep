## What is a ReplicaSet?

A **ReplicaSet** is a Kubernetes object that ensures a specified number of identical pod replicas are running at any given time. If a pod fails or is deleted, the ReplicaSet automatically creates a new pod to maintain the desired number of replicas. ReplicaSets are commonly used to guarantee high availability and scalability for applications.

**Key features:**
- Maintains a stable set of replica pods.
- Supports scaling up or down by adjusting the replica count.
- Uses label selectors to identify the pods it manages.
- Often used indirectly through Deployments, which manage ReplicaSets for rolling updates.

**Example YAML:**
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
    name: example-replicaset
spec:
    replicas: 3
    selector:
        matchLabels:
            app: myapp
    template:
        metadata:
            labels:
                app: myapp
        spec:
            containers:
            - name: myapp-container
                image: nginx
```
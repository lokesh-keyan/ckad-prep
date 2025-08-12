# Network Policies

## Introduction to Network Traffic

Imagine a scenario where:

- A **web server** serves the front-end to users
- An **API server** handles backend logic and communication
- A **database server** stores persistent data

### Traffic Flow

The traffic flow works as follows:

1. A user sends a request to the web server on port 80
2. The web server forwards the request to the API server on port 5000
3. The API server retrieves the required data from the database server on port 3306 and sends the response back to the user

### Traffic Types

There are two key types of traffic in this scenario:

- **Ingress Traffic**: Incoming traffic to a server (e.g., user requests reaching the web server)
- **Egress Traffic**: Outgoing traffic from a server (e.g., the web server forwarding a request to the API server)

> **Note**: When referring to ingress or egress traffic, only the direction in which the traffic originates is considered. Response traffic (often illustrated as dotted lines) is not directly controlled by these rules.

---

## Traffic Rules Example

For our setup, consider these traffic rules:

| Component | Traffic Type | Port | Description |
|-----------|--------------|------|-------------|
| **Web Server** | Ingress | 80 | Accepts HTTP requests from users |
| **Web Server** | Egress | 5000 | Forwards requests to the API server |
| **API Server** | Ingress | 5000 | Receives traffic from the web server |
| **API Server** | Egress | 3306 | Sends requests to the database server |
| **Database Server** | Ingress | 3306 | Accepts traffic from the API server |

---

## What Are Network Policies?

A network policy is a Kubernetes object that controls the traffic flow to and from Pods. Using labels and selectors, you can bind a network policy to one or more Pods, thereby restricting access to them. 

For example, you can set up a policy that only permits ingress traffic to the database Pod from the API Pod on port 3306.

### Key Concepts

- **Pod Selection**: Network policies target specific pods using label selectors
- **Traffic Control**: Define ingress and/or egress rules
- **Default Behavior**: Without network policies, all traffic is allowed
- **Isolation**: Network policies create pod isolation by default when applied

---

## Creating a Network Policy

To enforce a network policy, you use labels and selectors, much like linking ReplicaSets or Services to Pods. In our example, we want to allow only ingress traffic to the database Pod from the API Pod on port 3306.

### Example: Database Access Policy

Here is the complete YAML definition for our network policy:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          name: api-pod
    ports:
    - protocol: TCP
      port: 3306
```

### Configuration Breakdown

In this configuration:

- **`podSelector`**: Targets Pods labeled with `role: db`
- **`policyTypes`**: Specifies that this network policy controls ingress traffic
- **`ingress` rule**: Restricts access, allowing traffic only from Pods labeled with `name: api-pod` on TCP port 3306

---

## Network Policy Types

### Ingress Rules

Control incoming traffic to selected pods:

```yaml
spec:
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 80
```

### Egress Rules

Control outgoing traffic from selected pods:

```yaml
spec:
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 3306
```

### Combined Ingress and Egress

```yaml
spec:
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: database
```

---

## Advanced Selectors

### Namespace Selector

Allow traffic from specific namespaces:

```yaml
ingress:
- from:
  - namespaceSelector:
      matchLabels:
        name: production
```

### IP Block Selector

Allow traffic from specific IP ranges:

```yaml
ingress:
- from:
  - ipBlock:
      cidr: 192.168.1.0/24
      except:
      - 192.168.1.5/32
```

### Multiple Selectors

Combine different selector types:

```yaml
ingress:
- from:
  - podSelector:
      matchLabels:
        app: frontend
  - namespaceSelector:
      matchLabels:
        name: production
  - ipBlock:
      cidr: 10.0.0.0/8
```

---

## Network Policy Management Commands

### Creating and Managing Policies

```sh
# Apply network policy
kubectl apply -f network-policy.yaml

# List network policies
kubectl get networkpolicies
kubectl get netpol  # shorthand

# Describe network policy
kubectl describe networkpolicy db-policy

# Delete network policy
kubectl delete networkpolicy db-policy
```

### Testing Network Policies

```sh
# Test connectivity from one pod to another
kubectl exec -it frontend-pod -- curl http://backend-service:80

# Test with netcat
kubectl exec -it test-pod -- nc -zv database-service 3306

# Check if policy is working (should timeout or be refused)
kubectl exec -it unauthorized-pod -- curl http://protected-service:80
```

---

## Network Solutions Support

Not all network solutions support network policies. Here's the current support status:

### ✅ Supported Solutions

- **Calico**: Full network policy support
- **Cilium**: Advanced network policies with L7 support
- **Weave Net**: Basic network policy support
- **Antrea**: Full support with additional features
- **Kube-router**: Network policy implementation

### ❌ Unsupported Solutions

- **Flannel**: Does not support network policies (as of current version)

> **Important**: Always review your network solution's documentation to confirm support for network policies before implementing them in production.

---

## Best Practices

1. **Start with Allow-All**: Begin with permissive policies and gradually restrict
2. **Test Thoroughly**: Verify policies don't break legitimate traffic
3. **Use Labels Consistently**: Maintain clear labeling conventions
4. **Monitor Traffic**: Use logging to understand traffic patterns
5. **Document Policies**: Clearly document the purpose of each policy
6. **Regular Reviews**: Periodically review and update policies

---

## Common Patterns

### Deny All Traffic

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

### Allow Same Namespace

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-same-namespace
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector: {}
```

### Allow DNS

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-dns
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to: []
    ports:
    - protocol: UDP
      port: 53
```

---

## Troubleshooting Network Policies

### Common Issues

1. **Policy not enforced**: Check if CNI supports network policies
2. **Blocking legitimate traffic**: Review selector matches
3. **DNS resolution failing**: Ensure DNS egress is allowed

### Debugging Commands

```sh
# Check pod labels
kubectl get pods --show-labels

# Verify policy targets correct pods
kubectl get pods -l role=db

# Check network policy events
kubectl describe networkpolicy db-policy

# Test with temporary pods
kubectl run test-pod --rm -it --image=busybox -- /bin/sh
```
# Kubernetes Services

## Introduction to Services

Kubernetes services are essential as they enable seamless communication between various application components, both internally and externally. They make it possible for different parts of your application—for instance, front-end pods, back-end processors, and external data connectors—to interact without being directly coupled.

## The Networking Challenge

Imagine you've deployed a pod running a web application. While pods can communicate internally over the cluster network, external access presents a challenge. Consider this scenario:

- **Kubernetes node IP address**: `192.168.1.2`
- **Your laptop IP address**: `192.168.1.10` (on the same network)
- **Internal pod network**: `10.244.0.0/24` range
- **Pod IP**: `10.244.0.2`

Since your laptop is on a different network than the pod, directly pinging or accessing `10.244.0.2` won't work.

### Workaround Solution

One workaround is to SSH into the Kubernetes node and then access the pod using a command like:

```sh
curl http://10.244.0.2
```

This command, for example, might return:

```
Hello World!
```

---

## Types of Kubernetes Services

Kubernetes supports multiple service types:

| Service Type | Description | Example Use Case |
|--------------|-------------|------------------|
| **NodePort** | Exposes a pod on a port on each node | External access to a web server |
| **ClusterIP** | Creates a virtual IP inside the cluster to facilitate pod-to-pod communication | Inter-service communication between front-end and back-end |
| **LoadBalancer** | Provisions a load balancer from supported cloud providers for distributing traffic | Production environments requiring high availability |

---

## How NodePort Works

A NodePort service maps three key ports:

- **Target Port**: The port on the pod where the web server is running (e.g., port 80)
- **Service Port**: The port defined on the service object (typically also set to 80)
- **Node Port**: The port on the Kubernetes node used for external access (e.g., 30008)

### Port Mapping Flow

```
External Client → Node IP:NodePort → Service:Port → Pod:TargetPort
     ↓                ↓                ↓              ↓
192.168.1.10 → 192.168.1.2:30008 → Service:80 → Pod:80
```

---

## Creating a NodePort Service

To create a NodePort service, define its configuration in a YAML file. This file should include key components such as:

- API version
- Kind
- Metadata
- Spec (including service type, ports, and selectors)

Below are examples of how you can define a NodePort service and its associated pod.

### Service Definition (`service-definition.yml`)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: myapp
    type: front-end
```

### Pod Definition (`pod-definition.yml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

**Key Points:**
- The service is linked to the pod by matching the labels (`app: myapp` and `type: front-end`)
- The `selector` in the service must match the `labels` in the pod
- If `nodePort` is not specified, Kubernetes assigns a random port in the range 30000-32767

---

## Service Creation and Management Commands

### Creating Services

```sh
# Create service from YAML file
kubectl apply -f service-definition.yml

# Create service imperatively
kubectl expose pod myapp-pod --type=NodePort --port=80 --target-port=80

# Create service for deployment
kubectl expose deployment myapp-deployment --type=NodePort --port=80
```

### Managing Services

```sh
# List all services
kubectl get services
kubectl get svc  # shorthand

# Describe service details
kubectl describe service myapp-service

# Get service in YAML format
kubectl get service myapp-service -o yaml

# Delete service
kubectl delete service myapp-service
```

### Testing Service Connectivity

```sh
# Test from within cluster
kubectl exec -it myapp-pod -- curl http://myapp-service

# Test NodePort from external (if accessible)
curl http://<node-ip>:30008

# Port forward for local testing
kubectl port-forward service/myapp-service 8080:80
```

---

## Service Types in Detail

### ClusterIP (Default)

- **Purpose**: Internal cluster communication only
- **Access**: Only accessible from within the cluster
- **Use Case**: Database services, internal APIs

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  type: ClusterIP  # Can be omitted (default)
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: backend
```

### NodePort

- **Purpose**: External access through node ports
- **Port Range**: 30000-32767
- **Access**: `<NodeIP>:<NodePort>`

### LoadBalancer

- **Purpose**: Cloud provider load balancer
- **Requirements**: Supported cloud environment
- **Use Case**: Production external access

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
  selector:
    app: frontend
```

---

## Best Practices

1. **Use ClusterIP for internal services**: Reserve NodePort and LoadBalancer for external access
2. **Set appropriate labels**: Ensure service selectors match pod labels exactly
3. **Use meaningful names**: Choose descriptive service and port names
4. **Health checks**: Combine services with readiness probes
5. **Security**: Consider NetworkPolicies to restrict traffic flow
6. **Monitoring**: Track service endpoints and connectivity

---

## Troubleshooting Services

### Common Issues

1. **No endpoints**: Service selector doesn't match any pods
2. **Connection refused**: Pod not listening on target port
3. **Timeout**: Network policies or firewall blocking traffic

### Debugging Commands

```sh
# Check service endpoints
kubectl get endpoints myapp-service

# Verify pod labels
kubectl get pods --show-labels

# Test service resolution
kubectl exec -it myapp-pod -- nslookup myapp-service

# Check service events
kubectl describe service myapp-service
```

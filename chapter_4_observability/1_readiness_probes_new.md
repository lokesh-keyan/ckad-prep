# Readiness Probes

## Pod Lifecycle and States

When a pod is first created, it enters a **Pending** state while the scheduler selects an appropriate node for placement. If no node is immediately available, the pod stays in the Pending state. Use `kubectl describe pod` to investigate any delays.

After scheduling, the pod transitions to the **ContainerCreating** state as images are pulled and containers begin their startup process.

Once all containers launch successfully, the pod moves into the **Running** state and remains there until the application completes execution or is terminated.

---

## Pod Conditions

For more detailed insights, inspect the pod's conditions. Initially, when a pod is scheduled, the "PodScheduled" condition becomes true. Following initialization, the "Initialized" condition is set to true, and finally, once all containers are ready, the "ContainersReady" condition is affirmed. When all these conditions are true, the pod is officially considered ready.

### Pod Condition Flow:
1. **PodScheduled** → `true` (when pod is assigned to a node)
2. **Initialized** → `true` (after init containers complete)
3. **ContainersReady** → `true` (when all containers are ready)
4. **Ready** → `true` (when pod is ready to serve traffic)

---

## What are Readiness Probes?

Kubernetes relies on the pod's ready condition to determine if it should receive traffic. By default, it assumes that container creation equates to readiness. This is where readiness probes are critically important—they help signal the true readiness of the application within the container.

As an application developer, you can define tests (probes) that confirm the application has reached a ready state.

### Common Use Cases:

- **HTTP GET request** to check API responsiveness for a web application
- **TCP port check** for database services
- **Executing a custom command** that confirms the application is ready

---

## Configuring Readiness Probes

To configure a readiness probe, include the `readinessProbe` field in your pod specification. For an HTTP-based readiness probe, specify the endpoint path and port.

### Example: HTTP Readiness Probe

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    name: simple-webapp
spec:
  containers:
  - name: simple-webapp
    image: simple-webapp
    ports:
    - containerPort: 8080
    readinessProbe:
      httpGet:
        path: /api/ready
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 5
      failureThreshold: 8
```

---

## Readiness Probe Parameters

In addition to the probe type, you can also configure parameters such as:

- **`initialDelaySeconds`**: The delay before the first probe
- **`periodSeconds`**: How frequently the probe is executed
- **`failureThreshold`**: The number of consecutive failures that trigger the pod to be marked as not ready

### Additional Parameters:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `timeoutSeconds` | Number of seconds after which the probe times out | 1 |
| `successThreshold` | Minimum consecutive successes for the probe to be considered successful | 1 |

---

## Types of Readiness Probes

There are three primary types of readiness probes you can use:

### 1. HTTP GET Probe

Performs an HTTP GET request against the container's IP address on a specified port and path.

```yaml
readinessProbe:
  httpGet:
    path: /api/ready
    port: 8080
```

### 2. TCP Socket Probe

Performs a TCP check against the container's IP address on a specified port.

```yaml
readinessProbe:
  tcpSocket:
    port: 3306
```

### 3. Exec Command Probe

Executes a specified command inside the container. The probe is considered successful if the command exits with a status code of 0.

```yaml
readinessProbe:
  exec:
    command:
      - cat
      - /app/is_ready
```

---

## Best Practices

1. **Set appropriate timeouts**: Ensure probes don't time out too quickly for slow-starting applications
2. **Use meaningful health checks**: Design endpoints that truly reflect application readiness
3. **Consider startup time**: Set `initialDelaySeconds` based on your application's typical startup time
4. **Monitor probe failures**: Track probe failures to identify application issues
5. **Combine with liveness probes**: Use both readiness and liveness probes for comprehensive health monitoring

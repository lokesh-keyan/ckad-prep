# Logging in Kubernetes

## Logging in Docker

Imagine a Docker container named "event simulator" that produces random events to mimic a web server. This container sends these event logs to its standard output. For instance, executing the container in the foreground using the command below:

```sh
docker run kodekloud/event-simulator
```

### Example Output:

```
2018-10-06 15:57:15,937 - root - INFO - USER1 logged in
2018-10-06 15:57:16,943 - root - INFO - USER2 logged out
2018-10-06 15:57:17,944 - root - INFO - USER3 is viewing page3
2018-10-06 15:57:18,951 - root - INFO - USER4 is viewing page1
2018-10-06 15:57:19,954 - root - INFO - USER2 logged out
2018-10-06 15:57:20,955 - root - INFO - USER1 logged in
2018-10-06 15:57:21,956 - root - INFO - USER3 is viewing page2
2018-10-06 15:57:22,957 - root - INFO - USER4 is viewing page2
2018-10-06 15:57:23,959 - root - INFO - USER1 logged out
2018-10-06 15:57:24,963 - root - INFO - USER2 is viewing page2
2018-10-06 15:57:25,943 - root - INFO - USER4 is viewing page3
2018-10-06 15:57:26,965 - root - INFO - USER3 logged out
2018-10-06 15:57:27,965 - root - INFO - USER1 is viewing page2
2018-10-06 15:57:28,961 - root - INFO - USER4 is viewing page3
2018-10-06 15:57:29,967 - root - INFO - USER3 is viewing page1
2018-10-06 15:57:30,970 - root - INFO - USER4 logged in
2018-10-06 15:57:31,973 - root - INFO - USER1 is viewing page3
```

---

## Logging in Kubernetes Pods

### Single Container Pod

Start by creating the pod:

```sh
kubectl create -f event-simulator.yaml
```

Then, follow the logs using:

```sh
kubectl logs -f event-simulator-pod
```

### Pod Definition File (`event-simulator.yaml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: event-simulator-pod
spec:
  containers:
    - name: event-simulator
      image: kodekloud/event-simulator
```

---

## Multiple Containers in a Pod

Kubernetes allows you to create pods with multiple containers. Suppose you update your pod specification to include an additional container called "image-processor" as follows:

### Multi-Container Pod Definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: event-simulator-pod
spec:
  containers:
    - name: event-simulator
      image: kodekloud/event-simulator
    - name: image-processor
      image: some-image-processor
```

### Viewing Logs from Specific Container

When dealing with multi-container pods, you need to specify which container's logs you want to view:

```sh
kubectl logs -f event-simulator-pod event-simulator
```

This command will then output logs similar to:

```
2018-10-06 15:57:15,937 - root - INFO - USER1 logged in
2018-10-06 15:57:16,943 - root - INFO - USER2 logged out
2018-10-06 15:57:17,944 - root - INFO - USER2 is viewing page2
```

---

## Kubernetes Logging Commands

### Basic Logging Commands

| Command | Description |
|---------|-------------|
| `kubectl logs <pod-name>` | View logs from a single-container pod |
| `kubectl logs <pod-name> <container-name>` | View logs from a specific container in a multi-container pod |
| `kubectl logs -f <pod-name>` | Follow logs in real-time |
| `kubectl logs --tail=20 <pod-name>` | View last 20 lines of logs |
| `kubectl logs --since=1h <pod-name>` | View logs from the last hour |

### Advanced Logging Options

- **`--previous`**: View logs from a previous instance of the container (useful after crashes)
- **`--timestamps`**: Include timestamps in log output
- **`--tail=N`**: Show only the last N lines
- **`--since=TIME`**: Show logs since a specific time (e.g., `1h`, `30m`, `2006-01-02T15:04:05Z`)

### Example Commands

```sh
# View all logs from a pod
kubectl logs event-simulator-pod

# Follow logs in real-time
kubectl logs -f event-simulator-pod

# View logs from specific container in multi-container pod
kubectl logs event-simulator-pod -c event-simulator

# View last 50 lines with timestamps
kubectl logs --tail=50 --timestamps event-simulator-pod

# View logs from the last 30 minutes
kubectl logs --since=30m event-simulator-pod
```

---

## Best Practices for Logging

1. **Use structured logging**: Output logs in JSON format for better parsing
2. **Log levels**: Use appropriate log levels (DEBUG, INFO, WARN, ERROR)
3. **Centralized logging**: Consider using logging solutions like ELK stack or Fluentd
4. **Log rotation**: Ensure logs don't fill up disk space
5. **Security**: Don't log sensitive information like passwords or tokens

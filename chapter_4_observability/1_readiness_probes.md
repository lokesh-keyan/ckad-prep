When a pod is first created, it enters a Pending state while the scheduler selects an appropriate node for placement. If no node is immediately available, the pod stays in the Pending state. Use kubectl describe pod to investigate any delays.
After scheduling, the pod transitions to the ContainerCreating state as images are pulled and containers begin their startup process.
Once all containers launch successfully, the pod moves into the Running state and remains there until the application completes execution or is terminated.

For more detailed insights, inspect the pod’s conditions. Initially, when a pod is scheduled, the "PodScheduled" condition becomes true. Following initialization, the "Initialized" condition is set to true, and finally, once all containers are ready, the "ContainersReady" condition is affirmed. When all these conditions are true, the pod is officially considered ready.

Kubernetes relies on the pod’s ready condition to determine if it should receive traffic. By default, it assumes that container creation equates to readiness. This is where readiness probes are critically important—they help signal the true readiness of the application within the container.

As an application developer, you can define tests (probes) that confirm the application has reached a ready state. Examples include:

An HTTP GET request to check API responsiveness for a web application.
A TCP port check for database services.
Executing a custom command that confirms the application is ready.

To configure a readiness probe, include the readinessProbe field in your pod specification. For an HTTP-based readiness probe, specify the endpoint path and port. For example:

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

In addition to the probe type, you can also configure parameters such as:

initialDelaySeconds: The delay before the first probe.
periodSeconds: How frequently the probe is executed.
failureThreshold: The number of consecutive failures that trigger the pod to be marked as not ready.


There are three primary types of readiness probes you can use:

HTTP GET Probe:
readinessProbe:
  httpGet:
    path: /api/ready
    port: 8080

TCP Socket Probe:
readinessProbe:
  tcpSocket:
    port: 3306
Exec Command Probe:
readinessProbe:
  exec:
    command:
      - cat
      - /app/is_ready



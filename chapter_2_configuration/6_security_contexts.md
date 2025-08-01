# Security Contexts in Kubernetes

## Security in Docker vs Kubernetes

In Docker, you may run containers with security options like these:

```sh
docker run --user=1001 ubuntu sleep 3600
docker run --cap-add MAC_ADMIN ubuntu
```

In Kubernetes, containers run within Pods. You have the flexibility to set security contexts either at the **container level** or at the **Pod level**.

### Key Points:
- Settings defined at the **Pod level** affect all containers in that Pod
- If the same security context options are specified for both the Pod and individual containers, the **container-level settings override** those at the Pod level

---

## Example Pod Definition

Consider the following example of a Pod definition file. In this configuration, an Ubuntu container is started with the sleep command. The security context is defined within the container specification using the `securityContext` field.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep", "3600"]
      securityContext:
        runAsUser: 1000
        capabilities:
          add: ["MAC_ADMIN"]
```

### Security Context Parameters:
- **`runAsUser`**: Sets the user ID for the container
- **`capabilities`**: Adds specific Linux capabilities

---

## Practice

This example illustrates how to configure user permissions and capabilities in Kubernetes. Take some time to practice:

- **Viewing** security context configurations
- **Configuring** security contexts at Pod and container levels
- **Troubleshooting** security context issues using this configuration

We can define a set of securoty standands
- Containers are encapsulated in pods
- We can choose to confire it in pod level or container level
- The container level will override the settings in pod
- it is as simple as keeping the security context key in the pod yaml in pod level vs inside the container level

Most ideal set up
Set requests with no limits so all pods gets its own cpu and the other pods can take how much ever cpu they want without affecting the minimum required by another pod
# Kubernetes Pods

## Pods and Multi-Container Architecture

- A **Pod** is the smallest deployable unit in Kubernetes.
- A Pod can contain **one or more containers**.
- When a Pod has multiple containers, the additional containers are often called **sidecar** or **helper containers**.
- All containers in a Pod:
  - **Share the same network namespace** (they can communicate via `localhost`).
  - **Share the same storage volumes** (if defined in the Pod).
- This setup allows the containers to **collaborate tightly**, for example:
  - One container might serve an application.
  - Another container might collect logs or manage configuration updates.

> 🛰️ Multi-container Pods are useful when containers need to work closely together — not just for scaling.

## Deploying Pods in Kubernetes

### Creating a Pod

You can create a Pod using the `kubectl run` command:

```bash
kubectl run nginx --image=nginx
```

- This command creates a Pod named `nginx` and runs a container using the `nginx` Docker image.
- The `--image=nginx` flag tells Kubernetes which container image to use.
- By default, Kubernetes pulls the image from the **Docker Hub** registry.

### Where Are Images Pulled From?

- If no registry is specified, Kubernetes pulls from Docker Hub.
- You can configure Kubernetes to pull images from:
  - **Public registries** (like Docker Hub)
  - **Private registries** (using image pull secrets)

## Creating Pods with YAML

A basic Pod definition in Kubernetes is written using a YAML file. This YAML file includes **four required fields**:

### 1. `apiVersion`
- Specifies the version of the Kubernetes API to use for creating the object.
- Example: `v1`

### 2. `kind`
- Defines the type of Kubernetes object you want to create.
- For a Pod, this would be: `Pod`

### 3. `metadata`
- Contains metadata about the object such as `name`, `labels`, and `annotations`.
- It is a dictionary structure.
- Notes:
  - Indentation must be consistent — use spaces (not tabs).
  - Sub-fields must be indented more than their parent field.
  - Under `labels`, you can define any key-value pairs (user-defined).
  - `metadata` only accepts specific keys recognized by Kubernetes (e.g., `name`, `labels`).

### 4. `spec`
- Contains the specification for the Pod.
- Includes a `containers` list because a Pod can have multiple containers.
- Each container in the list is defined with:
  - `name`: A name for the container.
  - `image`: The Docker image to use.

### Example YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: demo
spec:
  containers:
    - name: nginx-container
      image: nginx
```

### Applying the YAML File

To create a Pod from the YAML file, use:

```bash
kubectl create -f pod-definition.yml
```

> ✅ Tip: You can also use `kubectl apply -f` for idempotent changes.

### Listing Pods

To list the running pods in your cluster:

```bash
kubectl get pods
```

- This command shows all pods in the current namespace.
- Use `-A` or `--all-namespaces` to view pods across all namespaces:

```bash
kubectl get pods --all-namespaces
```

```bash
kubectl get pods --all-namespaces
```
- This command shows detailed information about the Pod named my-pod. Think of it as an in-depth diagnostic report directly from the Kubernetes control plane. You will see the below:
- Name, Namespace, Node
- Labels and Annotations
- Status and Conditions
- Containers
- Events
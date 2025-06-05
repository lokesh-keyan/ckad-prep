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

### Listing Pods

To list the running pods in your cluster:

```bash
kubectl get pods
```

- This command shows all pods in the current namespace.
- Use `-A` or `--all-namespaces` to view pods across all namespaces:

```bash
kubectl get pods --all-namespaces
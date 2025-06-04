# Differences Between Docker and Other Container Runtimes

Docker was originally both a container runtime and an image builder, providing a full platform for building, shipping, and running containers. In Kubernetes, Docker served as the default container runtime for many years. However, other container runtimes such as **containerd**, **CRI-O**, and **rkt** have emerged. These alternatives are designed to be lightweight and focus solely on running containers, often adhering more closely to open standards. They are commonly used in Kubernetes environments for improved integration, performance, and maintainability.

| Feature         | Docker                | containerd / CRI-O / rkt         |
|-----------------|----------------------|----------------------------------|
| Image Building  | Yes                  | No (runtime only)                |
| Runtime Focus   | Full platform        | Lightweight, runtime only        |
| Kubernetes Use  | Deprecated (via dockershim) | Native CRI support        |
| Standards       | Docker-specific, now OCI-compliant | OCI-compliant           |

---

## CRI (Container Runtime Interface)

The **Container Runtime Interface (CRI)** is a standard API defined by Kubernetes. It allows different container runtimes to integrate with the kubelet (the Kubernetes node agent). CRI abstracts the details of the underlying container runtime, enabling Kubernetes to support multiple runtimes interchangeably.

---

## Dockershim

**Dockershim** was a component in Kubernetes that acted as a bridge between the CRI and the Docker runtime. It allowed Kubernetes to use Docker as its container runtime. As of Kubernetes 1.24, dockershim has been deprecated and removed, encouraging users to migrate to CRI-compatible runtimes like containerd or CRI-O.

---

## OCI (Open Container Initiative)

The **Open Container Initiative (OCI)** is an open governance structure for creating industry standards around container formats and runtimes. OCI defines specifications for container images and runtimes, ensuring interoperability between different tools and platforms in the container ecosystem.

---

## runc

**runc** is a lightweight, portable container runtime that implements the OCI runtime specification. It is responsible for creating and running containers according to the OCI standard. Many higher-level container runtimes, such as Docker and containerd, use runc under the hood to handle the low-level details of container lifecycle management. By adhering to the OCI specification, runc ensures compatibility and interoperability across different container platforms.

---

## ctr - Debugging tool

**ctr** is a low-level command-line interface provided by containerd for directly interacting with the containerd daemon. It is primarily intended for debugging, development, and advanced use cases rather than for general container management. With `ctr`, users can manage images, containers, snapshots, and namespaces, but it lacks the user-friendly features and ergonomics of higher-level tools.

- Not recommended for production workflows
- Useful for troubleshooting and exploring containerd internals

### Sample `ctr` Commands

Here are some basic examples of using `ctr` with containerd:

```sh
# List available images
sudo ctr images list

# Pull an image from a registry
sudo ctr images pull docker.io/library/nginx:latest

# List running containers
sudo ctr containers list

# Create a new container (does not start it)
sudo ctr containers create docker.io/library/nginx:latest mynginx

# Start a container
sudo ctr tasks start mynginx

# View running tasks (processes)
sudo ctr tasks list

# Stop a running container
sudo ctr tasks kill mynginx

# Remove a container
sudo ctr containers delete mynginx
```

These commands are intended for advanced users and troubleshooting scenarios.

---

## nerdctl

**nerdctl** is a Docker-compatible CLI for containerd. It provides a familiar user experience for Docker users, supporting commands like `run`, `build`, `compose`, and more. `nerdctl` bridges the gap between containerd and Docker, making it easier to adopt containerd without changing existing workflows.

- Supports most Docker CLI commands
- Enables rootless containers and Compose support
- Designed for developers and end-users seeking a Docker-like experience with containerd

### Sample `nerdctl` Commands

Here are some common examples of using `nerdctl` with containerd:

```sh
# Run a container interactively
nerdctl run -it --rm alpine sh

# List running containers
nerdctl ps

# List all containers (including stopped)
nerdctl ps -a

# Pull an image from a registry
nerdctl pull nginx:latest

# Build an image from a Dockerfile
nerdctl build -t myimage:latest .

# Start a container in detached mode
nerdctl run -d --name webserver -p 8080:80 nginx

# Stop a running container
nerdctl stop webserver

# Remove a container
nerdctl rm webserver

# Remove an image
nerdctl rmi nginx:latest

# Use Docker Compose (if installed)
nerdctl compose up -d
```

These commands provide a Docker-like experience for managing containers with containerd.

## crictl - Debugging tool

**crictl** is a command-line interface for CRI-compatible container runtimes such as containerd and CRI-O. It is primarily used for debugging and troubleshooting Kubernetes nodes by interacting directly with the container runtime through the CRI API. Unlike Docker or nerdctl, crictl is not intended for general container management but is invaluable for Kubernetes administrators and operators.

- Works with any CRI-compliant runtime (e.g., containerd, CRI-O)
- Useful for inspecting pods, containers, images, and runtime state on Kubernetes nodes
- Commonly used for troubleshooting when kubectl is insufficient or unavailable

### Sample `crictl` Commands

Here are some basic examples of using `crictl`:

```sh
# List all pods managed by the container runtime
sudo crictl pods

# List all containers
sudo crictl ps -a

# Inspect a specific container
sudo crictl inspect <container_id>

# View logs from a container
sudo crictl logs <container_id>

# Pull an image
sudo crictl pull nginx:latest

# Remove a container
sudo crictl rm <container_id>

# Remove an image
sudo crictl rmi nginx:latest
```

These commands help diagnose issues at the container runtime level, especially in Kubernetes environments.

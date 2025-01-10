# Docker Overview

## **Docker Image vs Docker Container**
- **Docker Image**:  
  A blueprint (static set of files and metadata) for creating containers. E.g., `ubuntu:latest` image.
  
- **Docker Container**:  
  A running instance created from a Docker image. It is a process running in an isolated environment using the image as a template.  
  - On Windows, Docker runs a lightweight Linux VM to utilize Linux features like namespaces and control groups.

## **ENTRYPOINT and CMD**
- **ENTRYPOINT**: Specifies the command that should always be executed when the container starts.
- **CMD**: Specifies default arguments for the entry point command.  
Both are set in the Dockerfile.

---

## **Starting Containers**
- **Create directory**:  
  `sudo mkdir -p /var/www/html`  
  - `-p` ensures intermediate directories are created.
  
- **Add content to file**:  
  `sudo sh -c "echo hello from docker >> /var/www/html/index.html"`  
  - `-c` allows the following string to be treated as a command.  
  - `>>` appends text to the file, while `>` overwrites.

- **Run a container**:  
  `docker run -d -p 8080:80 --name="myapache" -v /var/www/html:/var/www/html httpd`  
  - `-d` is for detached mode (runs in background).  
  - `-v` mounts a directory from the host machine into the container.  
  - The `httpd` image runs an Apache server, serving content on `localhost:8080`.

---

## **Managing Containers**
- **List all containers**:  
  `docker ps -a`  
  - `-a` shows all containers (running and stopped).
  
- **Start a container**:  
  `docker start <container_name>`
  
- **Stop a container**:  
  `docker stop <container_name>`  
  - Sends SIGTERM to stop the container.

- **Restart a container**:  
  `docker restart <container_name>`

- **Kill a container**:  
  `docker kill <container_name>`  
  - Sends SIGKILL to force stop the container.

- **Remove a container**:  
  `docker rm <container_name>`  
  - Removes container files from the host.

- **Inspect a container**:  
  `docker inspect <container_name>`  
  - Shows detailed container information in JSON format.

- **Find container PID**:  
  `ps aux` on the host to find the container's PID.

---

## **Understanding and Using Containers**
- **List all images**:  
  `docker images`

- **Get help with images**:  
  `docker images --help`

---

## **Container Logs**
- **View container logs**:  
  `docker logs <container_name>`  
  - Useful for troubleshooting container issues.

---

## **Lab 1 Solution**
- **Run Fedora container**:  
  `docker run -it --name fedora-container fedora:latest`  
  - `-it` allows interactive mode.
  
- **Explore container**:  
  ```bash
  cat /etc/os-release
  uname -r

# Docker Image Architecture

A container image is a tar file that contains multiple layers, along with associated metadata.

## **Layers in a Container Image:**
1. **Base System**: The first layer, which forms the foundation of the image.
2. **Application Layers**: Follow the base system layer, which includes the application or software.
3. **Pre-layered Images**: Some images are pre-layered, meaning that they come with base and application layers already built.

---

## **Build a Docker Image**

To build an image, use the following command:

```bash
docker build -t myapp:1.0 .
-t: Tags the image with a name and version (myapp:1.0).
.: Specifies the current directory as the build context (it must contain a Dockerfile).

---



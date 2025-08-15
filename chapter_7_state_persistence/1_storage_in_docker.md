# Docker Storage and Data Persistence

## Docker Data Storage on the Host

When Docker is installed, it organizes data within the `/var/lib/docker` directory. This folder contains several subdirectories such as `aufs`, `containers`, `images`, and `volumes`. Each subdirectory serves a specific role in Docker's architecture:

- **containers**: Stores all files related to running containers.
- **images**: Contains stored images.
- **volumes**: Holds data for persistent storage created by containers.

---

## Layered Architecture in Docker Images

Docker images use a layered architecture where each instruction in a Dockerfile creates a new layer capturing only the changes from the previous one.

```dockerfile
FROM Ubuntu

RUN apt-get update && apt-get -y install python

RUN pip install flask flask-mysql

COPY . /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
```

You can build this image with the following command:

```sh
docker build -t mmumshad/my-custom-app .
```

In this example:

- The base layer is the Ubuntu operating system.
- Subsequent layers add APT packages, Python packages, application source code, and finally, the entry point configuration.
- Each layer only stores the differences from its predecessor. For example, although the base Ubuntu image might be around 120 MB and the APT updates add an additional 300 MB, the remaining layers are much smaller. This strategy optimizes build times and minimizes disk space usage.

Consider a second application similar to the first, with the same base image and dependencies but a different source file and entry point:

```dockerfile
FROM Ubuntu

RUN apt-get update && apt-get -y install python

RUN pip install flask flask-mysql

COPY app2.py /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app2.py flask run
```

Build this image using:

```sh
docker build -t mmumshad/my-custom-app-2 .
```

Because the first three layers are identical across both applications, Docker reuses the cached layers and only builds the new layers corresponding to the changes.

---

## Understanding Image and Container Layers

A Docker image consists of several read-only layers:

- **Base Layer**: The Ubuntu operating system.
- **Packages Layer**: APT packages installed on top of Ubuntu.
- **Dependencies Layer**: Python packages such as Flask.
- **Source Code Layer**: Your application code included in the image.
- **Entry Point Layer**: The layer that sets the container's entry point.

When building the image:

```sh
docker build -t mmumshad/my-custom-app Dockerfile
```

The resulting layers remain read-only. Running a container from this image creates a new writable layer on top, which stores changes such as logs, temporary files, or modifications made during runtime. This mechanism is known as **copy-on-write**. Even if you modify a read-only file from the image, Docker creates a separate copy in the writable layer before applying the changes.

```sh
docker run mmumshad/my-custom-app
```

### CoW Analogy

Think of it like a library book:

- The library book itself is the base image. It's read-only and can be used by many people simultaneously.
- When a student wants to take notes on a page (modify a file), they don't write directly in the book. Instead, they make a photocopy of that page and write on the copy.
- The original book remains clean for others, while the student has their own personal, writable copy.

### Scenario

Let's imagine our base Docker image contains the Node.js application code and its dependencies.

**Node.js Web Server Container**: This is your main application. It needs to write to the filesystem, perhaps to create log files or cache data. When the container starts, it gets its own writable layer on top of the shared base image. When it writes logs to `/var/log`, a copy of the `/var/log` directory is created in its writable layer, and the logs are written there. The original `/var/log` in the base image remains untouched.

**Nginx Static File Server Container**: This container is used to serve static assets (like CSS, JavaScript, and images) that are also bundled within the base image. This container typically has read-only access to the base layers. It doesn't need to write to the filesystem. It simply reads the static files directly from the shared, read-only base image layers.

---

## Persisting Data with Volumes

For data persistence outside the container's ephemeral writable layer—such as database storage—use Docker volumes.

### Creating and Using Volumes

First, create a volume:

```sh
docker volume create data_volume
```

Then, mount the created volume when launching a container. For example, to store MySQL data in the volume, run:

```sh
docker run -v data_volume:/var/lib/mysql mysql
```

If a specified volume does not exist, Docker automatically creates it. To inspect the volumes, you can list the contents of `/var/lib/docker/volumes`.

### Bind Mounts

Alternatively, if you prefer using external storage (for instance, storing database files in `/data/mysql` on your host), you can use bind mounts:

```sh
docker run -v /data/mysql:/var/lib/mysql mysql
```

This technique maps a directory on the host to the container, enabling direct access to the host's filesystem.

### Using the --mount Option

The newer `--mount` flag provides a clearer and more explicit syntax. The equivalent bind mount example using `--mount` is:

```sh
docker run \
  --mount type=bind,source=/data/mysql,target=/var/lib/mysql \
  mysql
```

This syntax explicitly defines each parameter (type, source, target) and is recommended for its clarity.

---

## Docker Storage Drivers

Docker uses storage drivers to manage layered filesystems, the creation of writable layers, and copy-on-write operations. Common storage drivers include:

- **AUFS**
- **ZFS**
- **BTRFS**
- **Device Mapper**
- **Overlay**
- **Overlay2**

# Docker Practice Test

---

## 1. How many images are available on this host?

```sh
docker images
```

---

## 2. When a container is created using the image built with this Dockerfile, what is the command used to RUN the application inside it?

Inspect the Dockerfile in the `webapp-color` directory.

```dockerfile
ENTRYPOINT ["python", "app.py"]
```

---

## 3. Build a Docker image using the Dockerfile and name it `webapp-color` (no tag specified)

```sh
docker build -t webapp-color .
```

**Breakdown:**
| Part              | Meaning                                                      |
|-------------------|--------------------------------------------------------------|
| docker            | The Docker CLI tool                                          |
| build             | Command to build a Docker image                              |
| -t webapp-color   | Tags the resulting image with the name `webapp-color`        |
| .                 | Uses the Dockerfile in the current directory                 |

---

## 4. Run an instance of the image `webapp-color` and publish port 8080 on the container to 8282 on the host

```sh
docker run -p 8282:8080 webapp-color
```

**Breakdown:**
| Flag              | Meaning                                                      |
|-------------------|--------------------------------------------------------------|
| docker run        | Start a new container                                        |
| -p 8282:8080      | Map port 8282 on your host to port 8080 inside the container |
| webapp-color      | The name of the Docker image to run                          |

✅ After Running
You’ll be able to access your app in a browser at:

```
http://localhost:8282
```

Make sure your app inside the container is listening on port 8080 for this to work.

---

## 5. What is the base Operating System used by the `python:3.6` image?

If required, run an instance of the image to figure it out:

```sh
docker run -it python:3.6 cat /etc/os-release
```

**Breakdown:**
| Part         | Meaning                                         |
|--------------|-------------------------------------------------|
| docker run   | Runs a new container                            |
| -it          | Interactive mode with a terminal                |
| python:3.6   | The image to use                                |
| cat /etc/os-release | Command to display OS release information |


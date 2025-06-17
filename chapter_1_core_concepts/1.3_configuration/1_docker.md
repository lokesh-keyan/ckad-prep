Containerizing an application is essential for creating customized components that may not be available on Docker Hub, as well as streamlining deployment processes.

```sh
FROM ubuntu

RUN apt-get update && apt-get -y install python python-setuptools python-dev
RUN pip install flask flask-mysql

COPY . /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run --host=0.0.0.0
```
Dockerfile Breakdown
FROM: Sets the base image as Ubuntu. Every Docker image starts with a base layer from either an operating system or a prebuilt image available on Docker Hub.
RUN: Executes commands during image build. The first RUN updates the system and installs Python, while the second installs Flask and MySQL support via pip.
COPY: Transfers your application's source code from your local repository to the container directory /opt/source-code.
ENTRYPOINT: Specifies the command to execute when the container launches, setting the FLASK_APP environment variable and starting the Flask server.

Viewing Docker Image Layers
To inspect the image layers and their sizes, run the following command:
```sh
docker history <your-image-name>
```

Building the Docker Image
After finalizing your Dockerfile, build your image with the following command:

```sh
docker build -f Dockerfile -t mmumshad/my-custom-app .
```
During the build, Docker outputs details for each step. Here’s an example of what the build output might include:
```sh
root@osboxes:/root/simple-webapp-docker # docker build .
Sending build context to Docker daemon  3.072kB
Step 1/5 : FROM ubuntu
 ---> ccc7a11d65b1
Step 2/5 : RUN apt-get update && apt-get install -y python python-setuptools python-dev
 ---> Running in a7840bfad17
Get:1 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:4 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [46.3 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
Get:6 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [440 kB]
Step 3/5 : RUN pip install flask flask-mysql
 ---> Running in a4a6c9190ba3
Collecting flask
Downloading Flask-0.12.2-py2.py3-none-any.whl (83kB)
Collecting flask-mysql
Downloading Flask-MySQL-1.4.0-py2.py3-none-any.whl
Removing intermediate container a4a6c9190ba3
Step 4/5 : COPY app.py /opt/
 ---> 7cda1b1782
Removing intermediate container faaa63c512
Step 5/5 : ENTRYPOINT FLASK_APP=/opt/app.py flask run --host=0.0.0.0
 ---> Running in d452c574a8b8
 ---> 9f27c36920bc
Removing intermediate container d452c574a8b8
Successfully built 9f27c36920bc
```

Dockerfile	✅	Used to build container images
Docker CLI	✅	Commonly used for local builds and pushing images
Docker runtime	❌	Removed from Kubernetes in favor of containerd or CRI-O
Docker images	✅	Kubernetes runs OCI-compliant images, which Docker produces
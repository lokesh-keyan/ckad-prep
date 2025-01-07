Docker Image:
The ubuntu:latest image is just a blueprint. By itself, it is not running—it’s a static set of files and metadata.

Docker Container:
When you create and start a container from the ubuntu:latest image, Docker uses the image as a template and launches a process in an isolated environment (which is a linux OS functionality).
When we launch docker on windows it launches a lightweight Linux VM to make use of the Linux OS features like namespaces and control groups

ENTRYPOINT and CMD:
Docker images specify default instructions for what to run when a container starts.
These instructions are set in the image's Dockerfile using the CMD or ENTRYPOINT directives.

1.3 Starting containers
sudo mkdir -p /var/www/html
	-p to create intermediate directories if the don't exist
	/var/www is typically defacult directory where www files are stored

sudo sh -c "echo hello from docker >> /var/www/html/index.html"
	-c tells sh that treat following string as command.
	>> insert text into the file >> adds it to the end if the line, > overwrites.
	
docker run -d -p 8080:80 --name="myapache" -v /var/www/html:/var/www/html httpd
	-d is detached mode
	-v Volume mount option, mounts a directory from host machine into the container
	httpd is the image that the container is based on. The index.html runs on the apache server on the localhost:8080
	
1.4 Managing containers

	docker ps -a
		-a is for the past containers
	docker start 
		starts a container from a locally stored image
	docker stop
		stops a container using Linux SIGTERM
	docker restart
		restarts a currently running container
	docker kill
		stops a container using linux sigkill
	docker rm
		removes all container files from the host operating system
	docker inspect
		will show whats going on in json format
			--format allows you to filter out what you want to see
	ps aux on the host to find container pid
	
1.5 Understanding and using containers
	docker images
		to gat all images
	docker images --help 
		to get help with the images

1.6 Understanding container logs
	docker logs mycontainer
		to get access to the container log and convinient for troubleshooting
		
Lab 1 solution
	Run the latest version of Fedora in a container
		docker run -it --name fedora-container fedora:latest
			-it is interactive mode

	Start a bash shell and explore /etc/os-release file as well as Kernal version (uname -r)
		cat /etc/os-release
		uname -r
		
Image Architecture
	Container image is a tar file with associated metadata
	It consists of multiple layers
	We need a base system to build
	The application is installed as an additional layer
	Some of the images are already layered
	
docker build -t myapp:1.0 . => just builds an image, then use run to run it
	-t myapp:1.0: Names the image myapp and assigns it the 1.0 tag.
	.: Specifies the current directory as the build context (it must contain a Dockerfile)
	
Lab 2
create a dockerfile that creatres an image that
based on fedora, contains the packages containing the ps command as well as network tools and should run the sshd process

docker build -t fedora-sshd .
docker run -d -p 2222:22 --name my-fedora-sshd fedora-sshd
ssh root@localhost -p 2222



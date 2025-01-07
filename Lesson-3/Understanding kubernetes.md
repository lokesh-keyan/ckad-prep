K8s an open source system for automating deployment, scaling and managing of containerized applications
K8s comes from greek language, pilot of the ship
It is an ecosystem providing a core solution, with many third party add ons provided by approved projects
	Networking
	Ingress
	Monitoring
	Packaging

K8s Management Interfaces
	kubectl - command line utility provides convenient developer access, allowing you to run many tasks related to your applications
	curl allows developers to address the cluster using API calls from custom scripts
	K8s Dashboard can be installed to run on the k8s master node
	
Architecture
	Control node - maintained by AKS
		etcd
		c-manager
		api-server
		scheduler
		kubelet
		containerD 
	Worker node
		kubelet
		containerD
		
Deplyment takes care of the replica set and the replica set takes care of the pods

Understanding deployment options
	Minikube - contains some componenets otherwise hard to get it running
		Check remarkable page 4 for diagram
		This encapsulates both controller and worker nodes for us in one virtual machine, in a cloud provider this will be on managed by the cloud provider
			I have to see how minuikube does this on the computer 
		WHen you install minikube on Ubuntu, you need container layer or another vitual layer, so we do nested virtualization
		If you install minikube on host os, it might affect the host os
			So we need to install a vitual machine, and then containerize it. But why containerize the minikube ?
			
			go back to the questions before you continue 
			
Verifying Minikube
	minikube status
	kubectl get all
	minikube ssh
	docker ps: shows all docker processes on the minikube host
	
	
	
	
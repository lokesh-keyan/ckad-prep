# Kubernetes Overview (K8s)

## **What is Kubernetes?**
- **Kubernetes (K8s)**: An open-source system for automating the deployment, scaling, and management of containerized applications.
- The name "K8s" comes from the Greek word meaning "pilot of the ship" – symbolizing control and navigation.
- K8s provides a core solution for container orchestration, and there are many third-party add-ons provided by approved projects, including:
  - **Networking**
  - **Ingress**
  - **Monitoring**
  - **Packaging**

---

## **Kubernetes Management Interfaces**
1. **kubectl**:  
   A command-line utility that allows developers to manage Kubernetes clusters and perform tasks related to applications.
   
2. **curl**:  
   Used to address the Kubernetes API from custom scripts for automation.
   
3. **Kubernetes Dashboard**:  
   A web-based UI for monitoring and managing applications running in K8s, which can be installed and accessed on the master node.

---

## **Kubernetes Architecture**

1. **Control Plane (Managed by AKS)**:
   - **etcd**: Key-value store for all cluster data.
   - **c-manager**: Controls and manages the overall cluster state.
   - **api-server**: The interface to interact with the cluster (REST API).
   - **scheduler**: Schedules tasks and jobs to nodes.
   - **kubelet**: Ensures containers are running and healthy.
   - **containerD**: Container runtime responsible for running containers.

2. **Worker Node**:
   - **kubelet**: Ensures containers are running.
   - **containerD**: Container runtime for executing containers.

---

## **Kubernetes Deployment and Replica Sets**
- **Deployment**:  
  Manages and ensures the correct number of replicas of an application are running.
  
- **Replica Set**:  
  Ensures that a specific number of pod replicas are running at any given time. Managed by the Deployment.

---

## **Deployment Options**

- **Minikube**:
  - **Minikube**: A tool that sets up a local Kubernetes environment, useful for development and testing.
  - **Components**: Minikube includes components for both the control and worker nodes in a single virtual machine.
  
  - **Running Minikube**:
    - To run Minikube on Ubuntu, you need a container layer or a virtual layer (nested virtualization).
    - If you install Minikube directly on the host OS, it may affect the host environment, so a virtual machine is often used.
    - Minikube creates a virtual machine for Kubernetes, running the containerized environment inside.

  - **Verifying Minikube**:
    - `minikube status`: Check the status of your Minikube setup.
    - `kubectl get all`: Display all Kubernetes resources in your cluster.
    - `minikube ssh`: SSH into the Minikube VM.
    - `docker ps`: List Docker processes on the Minikube host.

---

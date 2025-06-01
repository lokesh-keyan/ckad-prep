
# Kubernetes Pod Lifecycle

## Overview
In Kubernetes, the lifecycle of a pod involves several key components working together to ensure that containers are properly scheduled and run. Here’s a brief explanation of the process:

### Lifecycle Steps:
1. **Controller**: Creates the Pod object based on the desired state (e.g., from a Deployment).
2. **Scheduler**: Assigns the Pod to an appropriate node.
3. **Kubelet**: On the assigned node, pulls the container image and starts the containers.
4. **Containers**: Begin running inside the Pod on the node.

### Visual Diagram:
![Kubernetes Pod Lifecycle](https://user-images.githubusercontent.com/123456789/123456789-abcdef.png)

---

## Detailed Explanation

### 1. Controller
The controller manager watches over the desired state of the cluster. When you create a Deployment, the controller manager ensures that the specified number of pods are running. If a pod dies, the controller manager creates a new Pod object.

### 2. Scheduler
The scheduler is responsible for assigning the Pod to a suitable node. It looks at the unscheduled pods and assigns them to nodes based on resource availability, taints/tolerations, affinity rules, etc.

### 3. Kubelet
Once the scheduler assigns a pod to a node, the kubelet on that node talks to the container runtime (like containerd or Docker) to pull the image and start the container. The kubelet ensures that the containers described in the PodSpec are running and healthy.

### 4. Containers
Containers are the actual instances of your application running inside the pod. They are started by the kubelet on the assigned node and are managed according to the specifications in the Pod object.

---

## Summary

| Component         | Responsibility                          | Creates Containers? |
|------------------|------------------------------------------|---------------------|
| **Scheduler**     | Assigns pods to nodes                    | ❌ No               |
| **Controller**    | Maintains desired state (e.g., Deployments) | ❌ No (creates Pods) |
| **Kubelet**       | Runs containers on assigned node         | ✅ Yes              |

---

## Author

**Lokesh Keyan**  
Senior Software Engineer | Kubernetes Enthusiast  
📍 Atlanta

---

## License

This project is licensed under the MIT License.

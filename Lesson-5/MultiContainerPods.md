# Multi Container Pods

- Always have only one container in one pod
    - Easier to maintain and configure
    - Lets say Container A and B are in POD-1, if you want to update container A then you have to bring the POD down which brings down the Container B as well
## Use cases for multi container pod
- **Sidecar container**: a container that enhances the primary application, for instance for logging
    - Example: Istio
- **Ambassador container**: a container that represents the primary container to the outside world, such as a proxy
- **Adapter container**: used to adopt the traffic or data pattern to match the traffic or data pattern in other aplication in the cluster

---


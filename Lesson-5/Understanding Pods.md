# Pods
    - An abstraction of a server
    - Can run multiple containers within a single namespace, exposed by a single IP address
    - A pod is not a Linux construct itself but a Kubernetes abstraction that orchestrates containers.

    Containers in the same pod:
        - Share the same network namespace: They use the same IP address and can communicate with each other via localhost.
    - Start only started through a deployment bcoz naked pods are not rescheduled in case of a node failure.
    
    Understanding pods for CKAD is big

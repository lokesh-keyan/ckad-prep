apiVersion: which version of the API
kind: indicates the type of object(deployment, pod, etc)
metadata: contains admin info about the object
spec: contains the specifics for the object
    name: name of the container
    image: the image that should be used
    command: the command the container should run
    args: arguments that are used by the command
    env: environment  variables that should be used by the container

"kubectl explain <pod, deployment>" - to get description on the specified component
"kubectl explain pod.spec" - to get more detauls on specs
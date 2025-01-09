apiVersion: which version of the API
kind: indicates the type of object(deployment, pod, etc)
metadata: contains admin info about the object
spec: contains the specifics for the object
    name: name of the container
    image: the image that should be used
    command: the command the container should run
    args: arguments that are used by the command
    env: environment  variables that should be used by the container

Excercise on this topic
- We have busybox.yaml from the repo
- run it using "kubectl create -f busybox.yaml"
- then use "kubectl get pods" to see the pods running
- delete and use apply to create the pods. 
    create - creates if it does not exist and throws an error if already created
    apply - creates if does not exist and updates if its already created
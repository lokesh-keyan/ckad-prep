**Understanding pods**    
    kubectl run
    kubectl run myginx --image=nginx
    kubectl get pods [-o yaml]
        "-o yaml" option provides insight in all Pod parameters
    kubectl describe pod <name> | less
        shows all details about a Pod and containers running within

**Understanding yaml**
    "kubectl explain <pod, deployment>"
        - to get description on the specified component
    "kubectl explain pod.spec | less"
        - to get more detauls on specs
    "kubectl explain --recursive pod.spec | less"
        - to get more detauls on specs

**Generating YAML Files**
    - "kubectl run mynginx --image=nginx -o yaml"
        - allows you to analyze the yaml file
    - "kubectl run mynginx --image=nginx -o yaml --dry-run=client -o yaml > mynginx.yaml"
        - allows you to create your own yaml file, but with limited options since we used dry run
        - The --dry-run=client ensures the Pod is not created in the cluster
        - The mynginx.yaml file contains the resource definition in YAML format, which you can inspect, modify, or use to create the Pod late

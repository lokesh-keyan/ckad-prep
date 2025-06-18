1. Create a pod using the file named ubuntu-sleeper-3.yaml. There is something wrong with it. Try to fix it!
Note: Only make the necessary changes. Do not modify the name.
```yaml
apiVersion: v1
kind: Pod 
metadata:
  name: ubuntu-sleeper-3
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - sleep
      - "1200"


kubectl create -f ubuntu-sleeper-3.yaml --dry-run=client
```



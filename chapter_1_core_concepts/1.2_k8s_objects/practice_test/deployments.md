## 1. Create a new Deployment using the deployment-definition-1.yaml file located at /root/.

```sh
kubectl explain deployment
change the file accordingly
check for kind, apiVersion, image name
```

## 2. Create a deployment
```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd-frontend
  template:
    metadata:
      labels:
        app: httpd-frontend
    spec:
      containers:
      - name: httpd
        image: httpd:2.4-alpine
        ports:
        - containerPort: 80
```
# Node Affinity Practice Test

## 1. How many labels are there in the node?

```sh
kubectl describe node node01
```

---

## 2. Apply a label `color=blue` to node `node01`

```sh
kubectl label node node01 color=blue
```

---

## 3. Create a new deployment named `blue` with the nginx image and 3 replicas

```sh
kubectl create deployment blue --image=nginx --replicas=3
```

---

## 4. Which nodes can the pods for the blue deployment be placed on?

Make sure to check taints on both nodes (if any)!

```sh
kubectl describe node node01 | grep Taints
kubectl describe node controlplane | grep Taints
```

---

## 5. Set Node Affinity to the blue deployment to place the pods on node01 only

**Requirements:**
- Ensure that node01 has the label `color=blue`
- Use `requiredDuringSchedulingIgnoredDuringExecution` node affinity
- Key: `color`
- Value: `blue`

If the label is not already set, apply it to node01 before updating the deployment.

> **Note:** When writing the affinity inside a deployment, go into the template level to apply it for the pods. Also the operator starts with upper case letters.

**Edit the deployment:**
```sh
kubectl edit deployment blue
```

**Add node affinity configuration:**
```yaml
spec:
  template:
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                - key: color
                  operator: In
                  values:
                    - blue
      containers:
      # ... existing container config
```

---

## 6. Which nodes are the pods placed on now?

```sh
kubectl get pods -o wide
```

---

## 7. Create a new deployment named `red` with the nginx image and 2 replicas, and ensure it gets placed on the controlplane node only

Use the label key `node-role.kubernetes.io/control-plane` - which is already set on the controlplane node.

**Create deployment with node affinity:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      app: red
  template:
    metadata:
      labels:
        app: red
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      affinity: 
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                - key: node-role.kubernetes.io/control-plane
                  operator: Exists
```

**Apply the deployment:**
```sh
kubectl apply -f red-deployment.yaml
```
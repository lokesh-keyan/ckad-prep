# Kubernetes Pods Practice Test

## 1. How many pods are there?

Use the following command to list all pods in the current namespace:

```sh
kubectl get pods
```

> **Note:**  
> `kubectl get pods --all-namespaces` will show system pods as well, which are not needed here.

---

## 2. Create a pod with the nginx image

```sh
kubectl run nginx --image=nginx
```

---

## 3. Check the image name of a pod

```sh
kubectl describe pod <pod_name>
```

---

## 4. What does the "READY" column in `kubectl get pods` indicate?

It shows:  
`<number of containers running in pod> / <total number of containers in pod>`

---

## 5. Delete a running pod

```sh
kubectl delete pod <pod_name>
```

---

## 6. Create a pod named `redis` with image `redis123` using a YAML file

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: redis
    labels:
        app: redis-app
spec:
    containers:
        - name: redis
            image: redis123
```

---

## 7. Edit the image name

1. Delete the old pod.
2. Edit the YAML file.
3. Recreate the pod using `kubectl apply`.

Example workflow:

```sh
kubectl run redis --image=redis123 --dry-run=client -o yaml > redis.yaml
vi redis.yaml   # Edit the image name as needed
kubectl delete pod redis
kubectl apply -f redis.yaml
```

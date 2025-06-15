# Imperative Commands Practice

## 1. Deploy a Pod named `nginx-pod` using the `nginx:alpine` image
```sh
kubectl run nginx-pod --image=nginx:alpine
```

---

## 2. Deploy a Redis Pod with label `tier=db`
**Option 1: Create directly with label:**
```sh
kubectl run redis --image=redis:alpine --labels="tier=db"
```
**Option 2: Generate YAML, edit, then apply:**
```sh
kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis-pod.yaml
```
Edit `redis-pod.yaml` to add labels:
```yaml
metadata:
  name: redis
  labels:
    tier: db
```
Then apply:
```sh
kubectl apply -f redis-pod.yaml
```

---

## 3. Create a Service `redis-service` to expose Redis on port 6379
```sh
kubectl expose pod redis --name=redis-service --port=6379 --target-port=6379 --type=ClusterIP
```
**Why use `--type=ClusterIP`?**
- `ClusterIP` is the default service type in Kubernetes and is used to expose a service internally within the cluster.
- Use when you want internal communication between pods or services (e.g., backend services like Redis or databases).
- It does **not** expose the service to the outside world (no external IP or port).

---

## 4. Create a Deployment named `webapp` with 3 replicas
```sh
kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3
```

---

## 5. Create a Pod `custom-nginx` running on container port 8080
```sh
kubectl run custom-nginx --image=nginx --port=8080
```

---

## 6. Create a Namespace `dev-ns`
```sh
kubectl create namespace dev-ns
```

---

## 7. Create a Deployment `redis-deploy` in `dev-ns` namespace with 2 replicas
```sh
kubectl create deployment redis-deploy --image=redis --replicas=2 --namespace=dev-ns
```

---

## 8. Create a Pod `httpd` and a Service of type ClusterIP
```sh
kubectl run httpd --image=httpd:alpine --port=80
kubectl expose pod httpd --name=httpd --port=80 --target-port=80 --type=ClusterIP
```
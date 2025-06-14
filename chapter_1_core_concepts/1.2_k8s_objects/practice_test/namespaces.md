## 1. How to get namespace in the cluster

```sh
kubectl get namespace
```

## 2. How many pods exist in the research namespace?

```sh
kubectl get pods --namespace=research
```

## 3. Create a pod in finance namespace with redis image

```sh
kubectl run redis --image=redis --namespace=finance
```

## 4. how to find which namespace has the blue pod in it

```sh
kubectl get pods -A
kubectl get pods --all-namespaces
kubectl get pods --all-namespaces | grep blue
```
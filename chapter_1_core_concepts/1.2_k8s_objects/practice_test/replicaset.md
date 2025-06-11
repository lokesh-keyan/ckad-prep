# ReplicaSet Practice Test

## 1. How many ReplicaSets exist in the system?

```sh
kubectl get replicasets
```

---

## 2. How many now?

```
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       23s
```

**Answer:** 1

---

## 3. What is the image name?

```sh
kubectl get replicaset new-replica-set -o yaml
```

---

## 4. Delete the ReplicaSet

```sh
kubectl delete replicaset <replicasetName>
```

---

## 5. Fix the original ReplicaSet

```sh
kubectl get replicaset new-replica-set -o yaml > rs-definition.yaml
vi rs-definition.yaml
```
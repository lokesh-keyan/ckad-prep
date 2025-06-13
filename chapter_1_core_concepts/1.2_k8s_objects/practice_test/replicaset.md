# ReplicaSet Practice Test

## 1. How many ReplicaSets exist in the system?

```sh
kubectl get replicaset
```

---

## 2. How many now?

```
kubectl get rs
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       23s
```

**Answer:** 1

---

## 3. What is the image name?

```sh
kubectl describe replicaset new-replica-set
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

kubectl edit rs new-replica-set
```

## 6. create replicaSet using the replicaset-definition-1.yaml in /root

```sh
kubectl create -f /root/replicaset-definition-1.yaml
kubectl explain replicaset
we can see the version
```
apiVersion should be apps/v1
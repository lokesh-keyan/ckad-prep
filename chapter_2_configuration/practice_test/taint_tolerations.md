# Taint and Tolerations Practice Test

## 1. How many nodes are in the cluster?

```sh
kubectl get nodes
```

**Expected output:**
```
NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   5m24s   v1.33.0
node01         Ready    <none>          4m47s   v1.33.0
```

---

## 2. Create a taint on node01 with key `spray`, value `mortein` and effect `NoSchedule`

```sh
kubectl taint nodes node01 spray=mortein:NoSchedule
```

---

## 3. Create a new pod with the nginx image and pod name as `mosquito`

```sh
kubectl run mosquito --image=nginx
```

> **Note:** This pod will remain in `Pending` state because it cannot tolerate the taint on node01.

---

## 4. Create another pod named `bee` with the nginx image, which has a toleration set to the taint `mortein`

**Generate the YAML:**
```sh
kubectl run bee --image=nginx --dry-run=client -o yaml > bee.yaml
```

**Edit `bee.yaml` to add toleration:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: "spray"
    operator: "Equal"
    value: "mortein"
    effect: "NoSchedule"
```

**Apply the pod:**
```sh
kubectl apply -f bee.yaml
```

---

## 5. Remove a taint

```sh
kubectl taint nodes node01 env:NoSchedule-
```

> **Note:** After removing the taint, any pod that was stuck in pending state due to the taint will get scheduled.

**To remove the `spray=mortein:NoSchedule` taint:**
```sh
kubectl taint nodes node01 spray=mortein:NoSchedule-
```

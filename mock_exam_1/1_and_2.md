# Mock Exam 1 - Questions 1 & 2

## Question 1: Multi-Container Pod

In the `ckad-multi-containers` namespace, create a pod named `tres-containers-pod`, which has 3 containers matching the below requirements:

- The first container named `primero` runs `busybox:1.28` image and has `ORDER=FIRST` environment variable.
- The second container named `segundo` runs `nginx:1.17` image and is exposed at port 8080.
- The last container named `tercero` runs `busybox:1.31.1` image and has `ORDER=THIRD` environment variable.

### Answer:

```bash
k run primero --image=busybox:1.28 --dry-run=client -o yaml > tres-containers-pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: tres-containers-pod
  namespace: ckad-multi-containers
spec:
  containers:
  - name: primero
    image: busybox:1.28
    env:
    - name: "ORDER"
      value: "FIRST"
    # ADD THIS COMMAND
    command: ["/bin/sh", "-c", "sleep 3600"]

  - name: segundo
    image: nginx:1.17
    ports:
    - containerPort: 8080

  - name: tercero
    image: busybox:1.31.1
    env:
    - name: "ORDER"
      value: "THIRD"
    # ADD THIS COMMAND
    command: ["/bin/sh", "-c", "sleep 3600"]
```

---

## Question 2: Storage Class

Create a storage class with the name `banana-sc-ckad08-str` as per the properties given below:

- Provisioner should be `kubernetes.io/no-provisioner`
- Volume binding mode should be `WaitForFirstConsumer`
- Volume expansion should be enabled

### Answer:

*Looked in the kubernetes documentation*

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: banana-sc-ckad08-str
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain # default value is Delete
allowVolumeExpansion: true
mountOptions:
  - discard # this might enable UNMAP / TRIM at the block storage layer
volumeBindingMode: WaitForFirstConsumer
parameters:
  guaranteedReadWriteLatency: "true" # provider-specific
```
# Encrypting Secrets in Kubernetes

> Reference: [Kubernetes Documentation - Encrypt Data](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)

## Understanding Secret Storage in etcd

Let's explore how Kubernetes Secrets are stored in the etcd database and how to encrypt them at rest.

### Creating a Test Secret

```sh
kubectl create secret generic my-secret --from-literal=key1=supersecret
```

### Viewing the Secret

```sh
kubectl get secret my-secret -o yaml
```

**Output:**
```yaml
apiVersion: v1
data:
  key1: c3VwZXJzZWNyZXQ=
kind: Secret
metadata:
  creationTimestamp: "2025-06-30T11:13:26Z"
  name: my-secret
  namespace: default
  resourceVersion: "1892"
  uid: f9530a2f-eca6-40c1-92c1-2d9adec95e02
type: Opaque
```

### Decoding the Base64 Value

```sh
echo "c3VwZXJzZWNyZXQ=" | base64 --decode
# Output: supersecret
```

---

## Installing etcdctl

To examine how data is stored in etcd server, we need to install the `etcdctl` client.

> **Note:** `etcdctl` is part of the etcd package but not available via apt directly. You'll need to download the binary.

### Step-by-Step Installation

**1. Download the latest etcd release:**
```sh
ETCD_VER=v3.5.12
curl -L https://github.com/etcd-io/etcd/releases/download/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o etcd.tar.gz
```

**2. Extract the tarball:**
```sh
tar xzvf etcd.tar.gz
cd etcd-${ETCD_VER}-linux-amd64
```

**3. Move the binaries to `/usr/local/bin`:**
```sh
sudo mv etcdctl /usr/local/bin/
```
> Optional: You can also move `etcd` if you're running an etcd server locally.

**4. Confirm it's working:**
```sh
etcdctl version
```

**Expected output:**
```sh
etcdctl version: 3.5.12
API version: 3.5
```

---

## Who Uses etcdctl?

`etcdctl` is primarily for cluster administrators who:

- **Run their own Kubernetes clusters** (e.g., using kubeadm, bare metal, or on cloud VMs)
- **Manage their own etcd** (the Kubernetes backing store)
- **Want to directly interact with etcd** for:
  - Taking backups
  - Restoring from disaster
  - Inspecting or debugging cluster state at a low level
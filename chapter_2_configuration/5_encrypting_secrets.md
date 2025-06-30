https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/

kubectl create secret generic my-secret --from-literal=key1=supersecret

controlplane ~ ➜  k get secret my-secret -o yaml
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

echo "c3VwZXJzZWNyZXQ=" | base64 --decode
supersecret

We are going to focus how this data us stored in etcd server?

Follow below steps to install etcdctl in playground or vm
To install the etcdctl client on a system that uses apt (like Ubuntu or Debian), follow these steps:

✅ Step-by-step: Install etcdctl
etcdctl is part of the etcd package — but not available via apt directly. You'll need to download the binary.

1. Download the latest etcd release
bash
Copy
Edit
ETCD_VER=v3.5.12

curl -L https://github.com/etcd-io/etcd/releases/download/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o etcd.tar.gz
2. Extract the tarball
bash
Copy
Edit
tar xzvf etcd.tar.gz
cd etcd-${ETCD_VER}-linux-amd64
3. Move the binaries to /usr/local/bin
bash
Copy
Edit
sudo mv etcdctl /usr/local/bin/
Optional: you can also move etcd if you're running an etcd server locally.

🧪 Confirm it's working
bash
Copy
Edit
etcdctl version
You should see something like:

bash
Copy
Edit
etcdctl version: 3.5.12
API version: 3.5

etcdctl is for cluster administrators who:

Run their own Kubernetes clusters (e.g., using kubeadm, bare metal, or on cloud VMs),

Manage their own etcd (the Kubernetes backing store),

Want to directly interact with etcd for:

Taking backups

Restoring from disaster

Inspecting or debugging cluster state at a low level
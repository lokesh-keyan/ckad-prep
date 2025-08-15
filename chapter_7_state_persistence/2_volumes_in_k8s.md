# Kubernetes Volumes and Persistent Storage

## A Simple Example with a Single-Node Cluster

Consider a simple implementation where a Pod generates a random number between 1 and 100 and writes it to `/opt/number.out`. Note that without a persistent volume, deleting the Pod also removes the generated number.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: random-number-generator
spec:
  containers:
    - image: alpine
      name: alpine
      command: ["/bin/sh","-c"]
      args: ["shuf -i 0-100 -n 1 >> /opt/number.out;"]
```

To retain the generated number, we attach a volume that leverages host storage. In this example, the host directory `/data` is used as the storage backend. When the volume is mounted inside the container at `/opt`, any file written there will be persisted on the host, even after the Pod is removed.

Below is the updated Pod specification with the volume properly configured:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: random-number-generator
spec:
  containers:
    - image: alpine
      name: alpine
      command: ["/bin/sh", "-c"]
      args: ["shuf -i 0-100 -n 1 >> /opt/number.out;"]
      volumeMounts:
        - mountPath: /opt
          name: data-volume
  volumes:
    - name: data-volume
      hostPath:
        path: /data
        type: Directory
```

In this configuration, the file `/opt/number.out` is stored on the host's `/data` directory. As a result, the data persists independently of the Pod's lifecycle.

---

## Storage Options for Volumes

The previous example utilized the `hostPath` option, which directly uses a directory on the host for persistent storage. While this works well in a single-node implementation, multi-node clusters typically require a more robust solution because the same directory structure is not guaranteed across different nodes.

Kubernetes supports various storage solutions beyond `hostPath`, including network-based options like NFS, GlusterFS, and Flocker, as well as block storage types such as Fibre Channel, CephFS, and ScaleIO. Moreover, public cloud providers offer native persistent storage options, for example:

- **AWS Elastic Block Store (EBS)**
- **Azure Disk**
- **Google Persistent Disk**

For instance, to configure an AWS EBS volume instead of using `hostPath`, the Pod specification is modified as follows:

```yaml
volumes:
  - name: data-volume
    awsElasticBlockStore:
      volumeID: <volume-id>
      fsType: ext4
```

With this configuration, the Pod utilizes AWS EBS for persistent storage, enabling reliable data management through the cloud provider's block storage services.

---

## Persistent Volumes and Persistent Volume Claims

In larger environments with numerous users and multiple pod deployments, manually configuring storage for each pod becomes both tedious and error-prone. Every time a storage configuration change is required, it would need to be updated on every pod individually. Persistent volumes solve this problem by centralizing storage management. Administrators can create a large pool of storage, and users can request specific portions from that pool using Persistent Volume Claims (PVCs).

A **PersistentVolume (PV)** is not a node itself. Instead, it's a Kubernetes API object that represents a piece of storage provisioned from an underlying storage system. That storage can be on a node (like a hostPath PV) or, more commonly, it can be on a shared network storage system like an NFS server, an AWS EBS volume, or a Google Cloud Disk.

A single PersistentVolume can only be mounted to a single Pod at a time if its accessMode is `ReadWriteOnce`. If its accessMode is `ReadOnlyMany` or `ReadWriteMany`, it can be mounted to multiple pods.

### Key Concepts

Here's a breakdown of the key concepts to clarify the relationship between Pods, PVs, and nodes:

- **PersistentVolume (PV)**: This is the storage itself, defined as a cluster-wide resource. It's like a raw disk partition that's ready to be used. An administrator typically creates these.

### The Relationship

Here's the relationship broken down simply:

- **PVC**: A formal request for a specific type of storage (e.g., 5GB of storage).
- **PV**: The actual piece of storage that satisfies the PVC's request.
- **Pod**: Your application container that uses the PVC to access the underlying PV.

Think of the PVC as a claim ticket for storage. The Pod holds the ticket, and Kubernetes uses the ticket to match the Pod to the actual storage. The ticket itself is not the Pod, and it's not the storage; it's just the object that links them together.

### Storage Analogy

Imagine your application is running inside a container, and it needs to save a file.

- **Your Computer**: This is your Kubernetes cluster.
- **The Pen Drive**: This is the PersistentVolumeClaim (PVC). You plug it into your computer.
- **The Internal Drive of the Pen Drive**: This is the PersistentVolume (PV), which represents the actual storage (e.g., a disk on a cloud provider like Google Cloud or AWS).
- **The Pod**: This is your application, which can now access the pen drive as a mounted volume.

---

## Creating a Persistent Volume Claim

Let's create a persistent volume claim using a YAML template. In this example, the API version is set to `v1` and the kind is `PersistentVolumeClaim`. The claim is named "myclaim". Under the specification, we set the access mode to `ReadWriteOnce` and request 500Mi of storage.

Below is the YAML definition for the PVC:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

To create the claim, run the following command:

```bash
kubectl create -f pvc-definition.yaml
```

Once applied, you can view the status of your PVC with:

```bash
kubectl get persistentvolumeclaim
```

The output might initially be similar to:

```
NAME      STATUS   VOLUME   CAPACITY   ACCESS MODES
myclaim   Pending
```

At this point, Kubernetes is evaluating the available persistent volumes. Suppose you have already configured a PV as shown below:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-voll
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  awsElasticBlockStore:
    volumeID: <volume-id>
    fsType: ext4
```

Since the PV's access mode matches and its capacity (1Gi) exceeds the PVC's request (500Mi), Kubernetes binds the PVC to this PV if no other closer match exists. You can verify the binding by executing:

```bash
kubectl get persistentvolumeclaim
```

The PVC status should now show as "Bound".

---

## Deleting a Persistent Volume Claim and Reclaim Policies

To delete a PVC, use the following command:

```bash
kubectl delete persistentvolumeclaim myclaim
```

After deletion, it's important to understand the fate of the underlying PV based on its reclaim policy. The reclaim policy determines what happens to a PV when its associated PVC is deleted.

### Reclaim Policy Options

The default reclaim policy is set to `Retain`, which means the PV remains available until manually deleted by an administrator:

```yaml
persistentVolumeReclaimPolicy: Retain
```

If you want Kubernetes to automatically delete the PV when the PVC is removed—thus freeing up the storage—you can change the reclaim policy accordingly. Alternatively, you might choose the `Recycling` policy, where the data on the volume is scrubbed before the volume is made available for reuse:

```yaml
persistentVolumeReclaimPolicy: Recycling
```

---

## Storage Classes Example

Let's say you have a MySQL database and a logging application, and you want them to use different storage backends.

### For the MySQL Database:

- An administrator creates a StorageClass named `fast-ssd` that uses a high-performance SSD-based provisioner.
- The MySQL Pod's manifest includes a PVC that requests a volume with `storageClassName: fast-ssd`.
- Kubernetes will then automatically provision a PV from the high-performance storage backend and bind it to the PVC.

### For the Logging Application:

- An administrator creates a different StorageClass named `slow-hdd` that uses a cheaper HDD-based provisioner.
- The logging application's Pod manifest includes a PVC that requests a volume with `storageClassName: slow-hdd`.
- Kubernetes will provision a PV from the slower storage backend.

This system ensures that each application gets the correct type of storage without the developer needing to know the specific details of the underlying cloud provider or storage system.

---

## Complete Workflow

Here's the full workflow in three simple steps:

1. **Administrator**: Creates one or more PV objects, making storage available to the cluster.
2. **Developer**: Creates a PVC object to request the storage.
3. **Pod**: The pod mounts the PVC as a volume, and Kubernetes automatically connects the pod to the underlying PV that satisfied the claim.

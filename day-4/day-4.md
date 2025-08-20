# 📘 OpenShift – Day 3: Storage in Kubernetes & OpenShift
## 1. Introduction

Storage is one of the most critical aspects in container orchestration. While
containers are ephemeral by design, most applications require persistent data.
Kubernetes (and OpenShift) provide a rich model to abstract storage resources
and make them usable in a portable way.

## 2. Core Concepts in Kubernetes

### ✅ Volume
Filesystem available to containers in a Pod; outlives a single
container restart but follows the Pod lifecycle. Multiple types (emptyDir,
configMap, CSI, etc.).

📘 [Kubernetes Volumes](https://kubernetes.io/docs/concepts/storage/volumes)

### ✅ PersistentVolume (PV)
Cluster‑scoped storage resource (backed by NFS, iSCSI, cloud disks, Ceph, …) with a lifecycle independent of any Pod. Can be statically or dynamically provisioned.
Statically provisioned means the cluster/storage administrator will create the PV manually.
Dynamically provision means the Storage Provisioner will create the PV automatically when a PVC requests a PV

📘 [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes)

### ✅ PersistentVolumeClaim (PVC)
A user’s request for storage (size + access mode + class). The control plane binds it to a matching PV.

📘 [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes)

### ✅ StorageClass
Describes how to dynamically provision PVs (parameters, QoS, reclaim policy,
default class, etc.). The StorageClass defines a Storage Backend and its
capabilities. It can also have a StorageProvisioner for creating the PVs
automatically when a PVC of the corresponding StorageClass is created.

📘 [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes)

### ✅ Access Modes
How a volume can be mounted in terms of read-write capabilities:
* **RWO** (ReadWriteOnce): one node (often databases).
* **ROX** (ReadOnlyMany): many nodes, read‑only.
* **RWX** (ReadWriteMany): many nodes, read/write (NFS, CephFS, etc.).

Matching rules: a claim that asks for RWO can bind to a volume that supports
RWO+ROX+RWX; you never get less than requested.

📘 [Access Modes](https://docs.redhat.com/en/documentation/openshift_container_platform/4.9/html/storage/understanding-persistent-storage)


### 🧠 Block storage vs File storage
#### Block storage (block-oriented)

* Data is stored and accessed in raw blocks (fixed-size chunks, e.g., 4KB).
* The system does not know about files or directories — it only sees blocks.
* A filesystem (ext4, XFS, NTFS…) must be created on top of the block device to organize files.
* Common in cloud volumes (AWS EBS, Azure Disk, GCP PD) and SANs.
* Use cases: Databases, VM disks, applications needing low latency and predictable performance.

👉 Analogy: A hard drive given “raw” to you — you decide the filesystem.

#### File storage (file-oriented)

* Data is exposed as a hierarchy of files and directories over the network.
* The remote system already manages blocks and the filesystem for you.
* Accessed via protocols like NFS, SMB, or CephFS.
* Multiple clients can share the same filesystem simultaneously.
* Use cases: Shared home directories, logs, ML datasets, media servers.

👉 Analogy: A shared network drive — you see files/folders, not blocks.

#### Quick comparison
| Aspect             | Block storage                      | File storage |
| ------------------ | ---------------------------------- | ------------------------------- |
| Access granularity | Raw blocks                         | Files & directories             |
| Needs filesystem?  | Yes, created by the user           | No, already provided by server  |
| Protocols          | iSCSI, Fibre Channel, cloud CSI    | NFS, SMB/CIFS, CephFS           |
| Sharing            | Usually one client per volume      | Many clients at once (RWX)      |
| Best for           | Databases, VM disks, transactional | Shared data, collaborative apps |


## 3. OpenShift Specifics

Default Storage Provisioner: Usually backed by CSI drivers (e.g., AWS EBS, Azure Disk, ODF/Ceph).

OpenShift Data Foundation (ODF): Based on Ceph, provides block, file, and object storage.

Integration with Cloud Providers: Direct mapping to managed storage services (EBS, AzureDisk, GCP PD).

## 4. Real-world Use Cases

Databases → Require RWO volumes for consistency.

Logging/Monitoring → Centralized storage with RWX to allow multiple consumers.

Machine Learning → Datasets stored in shared volumes, often RWX.

CI/CD Pipelines → Artifacts stored in persistent storage accessible by multiple
stages.

👉 Basically, every application that needs to maintain data on the FileSystem to
persist it even if the pod is restarted. In other words Stateful applications

## 5. 📘 VolumeSnapshots in Kubernetes

Snapshots allow you to capture the state of a PersistentVolume at a point in
time, similar to taking a "backup photo" of the data. This is managed through
CSI (Container Storage Interface) snapshot support.

There are three key objects:

### 1. VolumeSnapshotClass

The template/driver definition for how snapshots are created.

Similar to StorageClass but for snapshots.

Defines which CSI driver is used and the parameters (like snapshot location,
retention, encryption, etc.).

👉 Think: “rules of the game for creating snapshots.”

### 2. VolumeSnapshotContent

The actual snapshot resource, cluster-wide.

Created when a VolumeSnapshot is requested.

Binds to the VolumeSnapshot object (just like PersistentVolume binds to a PersistentVolumeClaim).

Can be pre-provisioned (admin creates first) or dynamically provisioned (created automatically by the CSI driver).

👉 Think: “the storage system’s saved snapshot artifact.”

### 3. VolumeSnapshot (for context)

The user-facing object (namespace-scoped).

A developer creates this object to request a snapshot of a PVC.

It binds to a VolumeSnapshotContent, using rules from the VolumeSnapshotClass.

👉 Think: “I want a snapshot of this PVC now.”

### Quick analogy

    StorageClass ↔ PV ↔ PVC

    VolumeSnapshotClass ↔ VolumeSnapshotContent ↔ VolumeSnapshot


## 5. CAP Theorem (Extra Content 🧠)

The CAP Theorem states that in distributed systems you can only guarantee two
out of three properties at the same time:

* Consistency (all clients see the same data at the same time)
* Availability (system is always available for requests)
* Partition Tolerance (system continues working despite network failures)

⚠️ This theorem is crucial to understand trade-offs in distributed storage and databases.

## 🧠 Exercise (Question)
**Question** How many types of `volumeBindingMode` are in a StorageClass, and
explain its differences

## 🧠 Exercise (Hands-on task)
Modify this deployment to include persistent storage to maintain the content of the file persistent even if the pod is restarted
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pvc-writer
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pvc-writer
  template:
    metadata:
      labels:
        app: pvc-writer
    spec:
      containers:
      - name: writer
        image: busybox:1.36
        command: ["/bin/sh","-c"]
        args:
          - >
            while true;
            do
              date >> /pvc/log.txt;
              sleep 10;
            done

```
1. Pod using emptyDir (ephemeral)
2. Pod using hostPath (node-local, but not portable)
3. Pod using a PVC with `gp3` StorageClass

## 🧠 Exercise (Hands-on task)
Migrate the Deployment from the previous exercise, to a StatefulSet, and explain
the differences

## 🧠 Exercise (Question)
**Question** Is it possible to remove a PVC if its being used by a Pod? Why?

## 🧠 Exercise (Question)
**Question** Are PVs Cluster-wide or Namespaced resources? Why?
**Question** Are PVCs Cluster-wide or Namespaced resources? Why?


```

                          +-------------------------+
                          |     Kubernetes Cluster  |
                          |                         |
                          |   +-----------------+   |
                          |   |   Persistent    |   |
                          |   |   Volumes (PV)  |   |
                          |   |  (cluster-wide) |   |
                          |   +-----------------+   |
                          |        |    |           |
                          +--------|----|-----------+
                                   |    |
        -----------------------------------------------------------------
        |                               |                               |
+-------------------+       +-------------------+       +-------------------+
|   Namespace A     |       |   Namespace B     |       |   Namespace C     |
|                   |       |                   |       |                   |
|  +-------------+  |       |  +-------------+  |       |  +-------------+  |
|  |   PVC A1    |  |       |  |   PVC B1    |  |       |  |   PVC C1    |  |
|  | (request)   |  |       |  | (request)   |  |       |  | (request)   |  |
|  +-------------+  |       |  +-------------+  |       |  +-------------+  |
|        |          |       |        |          |       |        |          |
|  +-------------+  |       |  +-------------+  |       |  +-------------+  |
|  |   Pod A     |  |       |  |   Pod B     |  |       |  |   Pod C     |  |
|  | uses PVC A1 |  |       |  | uses PVC B1 |  |       |  | uses PVC C1 |  |
|  +-------------+  |       |  +-------------+  |       |  +-------------+  |
+-------------------+       +-------------------+       +-------------------+


```

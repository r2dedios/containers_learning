# OpenShift – Day 1: Architecture and Core Concepts
---

## 1. OpenShift Architecture

OpenShift is a **Kubernetes-based container orchestration platform**, enriched with enterprise-grade features such as built-in CI/CD, advanced security policies, a web console, and Operators.

### 🔧 Core Components

- **API Server**: The entry point for all interactions. It exposes the RESTful Kubernetes API and handles validation, authentication, and persistence via etcd.
- **etcd**: A highly consistent, distributed key-value store. It holds the entire state and configuration of the cluster.
- **Scheduler**: Assigns pending pods to suitable nodes based on constraints and policies.
- **Controller Manager**: Hosts controllers like the Deployment Controller or ReplicaSet Controller. They implement reconciliation loops to enforce desired state.
- **Kubelet**: Runs on every node. It ensures containers are running as specified by the control plane.
- **CRI-O**: A lightweight container runtime interface implementation. It runs OCI-compatible containers without Docker.
- **CNI / SDN**: Networking plugins that provide inter-pod communication, service exposure, and network policies.

> 📘 Docs:
> - [OpenShift Architecture](https://docs.openshift.com/container-platform/latest/architecture/architecture.html)
> - [Kubernetes Control Plane Components](https://kubernetes.io/docs/concepts/overview/components/)

---

## 2. Why is there an API?

The Kubernetes **API Server** is the **central truth of the system**. Everything — from CLI commands to web consoles and automation tools — communicates with the cluster through this API.

### Benefits of having an API:

- Enables a **declarative model**: users describe *what* they want, not *how* to do it.
- Promotes extensibility and modularity.
- Provides a single point for **authentication, authorization, and auditing**.
- Powers automation, GitOps, and Operator-based workflows.

> 📘 Docs:
> - [Kubernetes API Server](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver)
> - [OpenShift REST API Reference](https://docs.openshift.com/container-platform/latest/rest_api/index.html)

---

## 3. Why etcd? And why 3 replicas?

**etcd** is a distributed, consistent key-value store used as Kubernetes’ backend database. It stores all cluster data: pod specs, configurations, secrets, service endpoints, and more.

### Key Characteristics:

- Built with the [**Raft consensus algorithm**](https://raft.github.io/) for fault tolerance and consistency.
- Stores both desired and current state of the cluster.
- It is a **critical component** — losing etcd data means losing the cluster's state.

### Why 3 replicas?

- To achieve **quorum-based consensus**, etcd must have a majority of nodes available.
- With 3 replicas:
  - The system tolerates 1 failure (quorum is 2 of 3).
  - Ensures high availability without excessive complexity.
- Using only 2 replicas can lead to split-brain scenarios.

> 📘 Docs:
> - [OpenShift etcd Backup and Restore](https://docs.openshift.com/container-platform/latest/backup_and_restore/control_plane_backup_and_restore/overview.html)
> - [Kubernetes etcd Guide](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)

---

## 4. The Reconciliation Loop

Kubernetes uses **reconciliation loops** to continuously ensure the actual state of the cluster matches the desired state defined by the user.

### What happens when you apply a manifest?

1. A user runs `oc apply -f deployment.yaml` or similar.
2. The API server validates and persists the manifest in etcd.
3. A controller (e.g., Deployment Controller) detects the new desired state.
4. The controller compares the desired state with the current state.
5. If they differ, the controller takes action (e.g., creates Pods, changes ReplicaSets).

This is **eventually consistent**, not instantaneous. It’s safe, observable, and resilient.

> 📘 Docs:
> - [Kubernetes Controllers](https://kubernetes.io/docs/concepts/architecture/controller/)
> - [Reconciling State – Argo Blog](https://blog.argoproj.io/reconciling-state-in-kubernetes-6fefc3060c53)

---

## 5. Node Types and Scheduling

### 🖥️ Node Roles:

- **Control Plane (Master Nodes)**: Run Kubernetes core components (API Server, etcd, Scheduler, Controllers).
- **Worker Nodes**: Execute user workloads (i.e., Pods).
- **Infra or Edge Nodes**: Handle specific workloads like routers, monitoring, or edge computing.

Node roles are defined through **labels and taints**.

### ⚖️ Scheduling Process

1. Pods in `Pending` state are evaluated by the Scheduler.
2. The Scheduler considers:
   - Resource availability (CPU, memory)
   - Node labels and affinities
   - Taints and tolerations
   - Topology constraints
3. It assigns the pod to a node.
4. The kubelet on that node pulls the image and starts the container.

> 📘 Docs:
> - [Pod Scheduling](https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/)
> - [Node Affinity and Taints](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)

---

## 6. Basic workloads

In Kubernetes, **Workloads** are API objects that manage and run containers.  They define how applications are deployed, scaled, and updated over time.

### Deployment

A `Deployment` is a Kubernetes resource that declaratively manages the lifecycle
  of application Pods. It ensures that the specified number of replicas are
  running and enables rolling updates and rollbacks.

  **Key Characteristics:**
  - Defines the desired state of Pods and ReplicaSets.
  - - Supports rolling updates with zero downtime.
  - - Enables rollback to previous versions.
  - - Automatically replaces failed Pods.

📘 [Deployment – Kubernetes Docs](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)


### ReplicaSet

A `ReplicaSet` ensures that a specified number of identical Pods are running at
  all times. It is primarily used by Deployments and rarely created directly.

  **Key Characteristics:**
  - Maintains the correct number of replicas.
  - - Replaces Pods if they fail or are deleted.
  - - Selected by matching `labels`.
📘 [ReplicaSet – Kubernetes Docs](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)


### Pod

A `Pod` is the smallest deployable unit in Kubernetes. It encapsulates one or
  more containers that share storage, network, and a specification for how to
  run the containers.

  **Key Characteristics:**
  - Atomic unit of deployment.
  - - Containers in the same Pod share IP and localhost.
  - - Lifecycle is managed as a single entity.
  - - Typically ephemeral by nature.
📘 [Pods – Kubernetes Docs](https://kubernetes.io/docs/concepts/workloads/pods/)

### Container

A `Container` is a lightweight, isolated runtime environment for a single
  process. In Kubernetes, containers run inside Pods and are managed by a
  container runtime like CRI-O or containerd.

  **Key Characteristics:**
  - Encapsulates application code, dependencies, and environment.
  - - Fast to start, portable, and resource-efficient.
  - - Stateless and isolated from the host system.
📘 [Containers Overview – Kubernetes
Docs](https://kubernetes.io/docs/concepts/containers/)

### ConfigMap

A `ConfigMap` is a Kubernetes object used to inject configuration data into
  Pods. It decouples configuration from application code.

  **Key Characteristics:**
  - Stores key-value pairs.
  - - Can be consumed as environment variables, command-line args, or mounted
      volumes.
      - Not intended for sensitive data.
📘 [ConfigMap – Kubernetes Docs](https://kubernetes.io/docs/concepts/configuration/configmap/)

### Secret

A `Secret` is a Kubernetes object used to securely store and manage sensitive
  information, such as passwords, tokens, and SSH keys.

  **Key Characteristics:**
  - Stores base64-encoded key-value data.
  - - Can be mounted as files or injected as environment variables.
  - - More secure than ConfigMaps due to controlled access.
📘 [Secret – Kubernetes
Docs](https://kubernetes.io/docs/concepts/configuration/secret/)



## 🧠 Exercise (Discussion)

**Question:**
Try to figure out which services could be re-scheduled on the Infrastructure nodes and explain why

**Expected Answer:**
1. Ingress routers/controller
2. Internal Image registry
3. Monitoring components
4. Logging components
5. Openshift Console
6. External Operators (like ACM, ACS, GitOps, ...)

Basically, every Openshift component that extends the infrastructure capabilities of the cluster but it's not part of the CORE components of the cluster


## 🧠 Exercise (Research task)
**Question:**
Research about how Openshift configures the role of the nodes. In other words, how we configure in Openshift a node is Worker, Master or Infra

## 🧠 Exercise (Hands-on task)
### Step 1: Deployment
**Assignment description**
Create a deployment (using the declarative way, in other words, manifests) in
your personal namespace, that configures the following parameters for your
workload:
1. Deployment name: `day-1-deploymenyt`
2. Image: `registry.redhat.io/ubi9/ubi:9.6-1753769805`. (Check this
   [link](https://access.redhat.com/articles/RegistryAuthentication#:~:text=To%20login%20to%20the%20registry,article%20with%20the%20podman%20command.)
   for logging on `registry.redhat.io`)
3. Add the following labels to your deployment:
    ```yaml
    owner: <YOUR_NAME>
    lesson: day-1
    ```
4. Add the following labels to the Pod template in the Deployment
    ```yaml
    owner: <YOUR_NAME>
    lesson: day-1
    purpose: testing
    version: v1
    ```
5. The pods' name is `learning-pod`
6. The pod must run this custom command:
    ```sh
    if [ -z $CONQUEROR_NAME ]; then
      echo "Env Var 'CONQUEROR_NAME' is not defined"
      exit
    fi

    while true; do
      echo "This is the pod '$CONQUEROR_NAME' running on '$HOSTNAME'"
      sleep 10
    done
    ```
7. The deployment deploys 1 replicas
8. The deployment defines environment vars in the Pod' templates
    ```sh
    CONQUEROR_NAME="Napoleon Bonaparte"
    ```
9. The pods' `restartPolicy` parameter is `Always`


**Questions**:
1. What's the difference between the pod's labels and deployment's labels?
2. What the pod does if we remove the custom command?
3. Now remove the `env` section and apply the changes. Why it fails? How to fix it? Why there are two pods, one failing and the other one running?
5. Describe what happens if you **update** the EnvVars in the deployment
6. What `restartPolicy` means?
7. What happens if you remove the pod? `oc delete pod/<POD_NAME>`


### Step 2: ConfigMap
Create a ConfigMap and learning how to connect it with a Deployment

1. Create a ConfigMap called `day-1-configmap` in the same namespace and labels as the Deployment from **Step-1**
2. The ConfigMap must contain this environment var
    ```sh
    CONQUEROR_NAME="Napoleon Bonaparte"
    ```
3. Mount the ConfigMap in your deployment for loading the environment variable from the ConfigMap and not from the Deployment `env` section.
4. Edit the ConfigMap value
    ```sh
    CONQUEROR_NAME="Francisco Pizarro"
    ```

**Questions**:
1. How the Pod is obtaining the values from the ConfigMap?
2. What happens when you update the ConfigMap? Is the pod also updated? Is there any step missing?
3. Now remove the ConfigMap and re-create it in the `default` namespace and then remove the pod `oc delete pod/<POD_NAME>`. Describe what happens
4. Can you edit the ConfigMap content from the pods' running instance?


### Step 3 (Extra): Secret
1. Move the EnvVar from the ConfigMap to a Secret called `day-1-secret`
2. Modify the Deployment to load the vars from the Secret instead of the
   ConfigMap

**Questions**:
1. What's the real difference between a ConfigMap and a Secret?
2. Are Secrets safer? If yes, explain why

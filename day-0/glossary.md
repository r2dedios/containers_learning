# 🔹 Glossary

---

### **Container**
A lightweight, standalone, and executable package that includes an application and all its dependencies. It runs in isolation but shares the host OS kernel.

📘 [What is a Container – Docker](https://www.docker.com/resources/what-container/)

---

### **Image**
A read-only template used to create containers. It includes the OS libraries, dependencies, and the application code.

📘 [Images – Docker Docs](https://docs.docker.com/get-started/02_our_app/)

---

### **Pod**
The smallest deployable unit in Kubernetes. A pod encapsulates one or more containers that share the same network namespace and storage volumes.

📘 [Pods – Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/)

---

### **Cluster**
A group of machines (nodes) running Kubernetes that are managed as a single system. Contains control plane and worker nodes.

📘 [Cluster Architecture – Kubernetes](https://kubernetes.io/docs/concepts/architecture/)

---

### **Node**
A single machine (physical or virtual) in a Kubernetes cluster. There are two types: control plane nodes and worker nodes.

📘 [Nodes – Kubernetes](https://kubernetes.io/docs/concepts/architecture/nodes/)

---

### **Control Plane**
The set of components that manage the cluster. It makes global decisions (scheduling, scaling) and detects/responds to cluster events.

📘 [Control Plane – Kubernetes](https://kubernetes.io/docs/concepts/overview/components/)

---

### **kubelet**
An agent that runs on each node. It ensures containers are running in a pod as expected.

📘 [kubelet – Kubernetes](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/)

---

### **API Server**
The front-end of the Kubernetes control plane. It handles all REST operations for managing cluster resources.

📘 [kube-apiserver – Kubernetes](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver)

---

### **etcd**
A distributed key-value store used to persist the entire state of the Kubernetes cluster.

📘 [etcd – Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)

---

### **Controller**
A loop that watches the cluster state and attempts to move it toward the desired state by taking action when needed.

📘 [Controllers – Kubernetes](https://kubernetes.io/docs/concepts/architecture/controller/)

---

### **Scheduler**
Assigns newly created pods to suitable nodes based on resource availability and constraints.

📘 [Scheduler – Kubernetes](https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/)

---

### **Deployment**
A higher-level object that manages ReplicaSets and Pods. It allows declarative updates to applications.

📘 [Deployments – Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

---

### **ReplicaSet**
Ensures a specified number of pod replicas are running at any given time.

📘 [ReplicaSets – Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

---

### **Service**
A stable network endpoint that exposes a set of Pods. It abstracts away dynamic pod IPs.

📘 [Services – Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/)

---

### **Namespace**
A logical partition within a Kubernetes cluster to isolate resources between teams, environments, or applications.

📘 [Namespaces – Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

---

### **ConfigMap**
Used to inject configuration data into pods at runtime in a decoupled way.

📘 [ConfigMaps – Kubernetes](https://kubernetes.io/docs/concepts/configuration/configmap/)

---

### **Secret**
Stores sensitive data like passwords or tokens in base64 format, separate from application code.

📘 [Secrets – Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/)

---

### **CRI-O**
A lightweight container runtime for Kubernetes optimized for security and performance. Used by OpenShift.

📘 [CRI-O – GitHub](https://github.com/cri-o/cri-o)

---

### **CNI**
Container Network Interface. Plugins that manage networking for containers and pods.

📘 [CNI Plugins – GitHub](https://github.com/containernetworking/plugins)

---

### **OpenShift**
An enterprise Kubernetes distribution by Red Hat that includes additional tooling for developers, security, and operations.

📘 [What is OpenShift? – Red Hat](https://www.redhat.com/en/technologies/cloud-computing/openshift)

---

### **OKD**
The open-source upstream version of OpenShift, community-maintained.

📘 [OKD – Official Site](https://www.okd.io/)

---

### **Operator**
A method of packaging, deploying, and managing Kubernetes applications using custom resources and controllers.

📘 [Operators – OpenShift Docs](https://docs.openshift.com/container-platform/latest/operators/understanding/olm-what-operators-are.html)

---

### **Helm**
A package manager for Kubernetes that simplifies deployment of applications using charts (templated YAMLs).

📘 [Helm – Official Site](https://helm.sh/)

### **Master Node**
A node that hosts the Kubernetes control plane components: API Server, Scheduler, Controller Manager, and etcd. It does not run user workloads directly.

📘 [Control Plane Components – Kubernetes](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components)

---

### **Worker Node**
A node that runs application Pods and is managed by the control plane. It contains the `kubelet`, a container runtime (e.g. CRI-O), and the `kube-proxy`.

📘 [Nodes – Kubernetes](https://kubernetes.io/docs/concepts/architecture/nodes/)

---

### **Infrastructure Node**
A node in OpenShift dedicated to running infrastructure-related workloads such as the router, registry, monitoring, or logging components. It is typically tainted to avoid scheduling regular application Pods.

📘 [Infrastructure Nodes – OpenShift](https://docs.openshift.com/container-platform/latest/nodes/nodes/nodes-nodes-managing.html#nodes-nodes-infra_nodes-nodes-managing)

---

### **SDN (Software-Defined Networking)**
An approach to networking where the control plane is decoupled from the data plane, allowing centralized and programmable network management. In Kubernetes/OpenShift, SDNs manage Pod-to-Pod and Pod-to-Service communication.

📘 [OpenShift SDN – Documentation](https://docs.openshift.com/container-platform/latest/networking/networking.html)

---

### **Controller Manager**
A daemon that runs controllers in Kubernetes. Each controller watches the state of the cluster and makes changes to reach the desired state. Examples include Node Controller, ReplicaSet Controller, and Job Controller.

📘 [Controller Manager – Kubernetes](https://kubernetes.io/docs/concepts/architecture/controller/)

---

### **Ingress**
An API object that defines rules for routing external HTTP(S) traffic to services within the cluster. It typically uses an Ingress Controller to handle the routing.

📘 [Ingress – Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/)

---

### **Egress**
Refers to outbound traffic from the cluster to external networks. Kubernetes and OpenShift can control egress traffic using egress policies, firewalls, and proxies.

📘 [Egress Traffic Control – Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/#egress-rules)

---

### **Volume**
A directory accessible to containers in a Pod, used for persistent or ephemeral data. Volumes outlive individual container restarts within a Pod.

📘 [Volumes – Kubernetes](https://kubernetes.io/docs/concepts/storage/volumes/)

---

### **Persistent Volume (PV)**
A piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using a StorageClass. PVs are used to persist data beyond the lifecycle of Pods.

📘 [Persistent Volumes – Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

---

### **Volume Binding (Docker/Podman context)**
In Docker or Podman, volume binding refers to the practice of mounting a host directory or volume into a container. This is used to persist data or provide configuration files from the host to the container.

📘 [Bind Mounts – Docker](https://docs.docker.com/storage/bind-mounts/)
📘 [Volumes – Podman](https://docs.podman.io/en/latest/markdown/podman-run.1.html#volume-v)

---

### **kubelet**
The primary node agent that runs on each Kubernetes node. It ensures the containers are running according to the PodSpec sent by the API Server.

📘 [kubelet – Kubernetes](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/)

### **OVN-Kubernetes**
OVN-Kubernetes is the default CNI (Container Network Interface) plugin in OpenShift that provides a virtual networking layer using Open Virtual Network (OVN) to manage Pod-to-Pod, Pod-to-Service, and ingress/egress traffic. It supports advanced features like network policies, egress IPs, and dual-stack networking.


📘 [OVN-Kubernetes](https://ovn-kubernetes.io/)

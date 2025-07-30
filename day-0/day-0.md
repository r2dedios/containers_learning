# Day 0: Introduction. What is Kubernetes (K8s)? What is Openshift(RHOCP)? What are the differences?

## What are containers?

Containers are lightweight, portable units that package an application and its dependencies together. They share the host OS kernel but run in isolated user spaces.

### Glossary

Before continuing, check this document for getting definitions of the concepts
explained during this lesson. [Glossay](./glossary.md)

### Key Features

- **Isolation**: Uses namespaces and cgroups to isolate processes.
- **Portability**: Build once, run anywhere (as long as a container runtime is available).
- **Efficiency**: Faster startup times and lower resource overhead than virtual machines.

> 📘 Docs:
> - [What is a Container – Docker](https://www.docker.com/resources/what-container/)
> - [Containers Overview – Red Hat](https://www.redhat.com/en/topics/containers)

---

## 1. What is a Distributed System in This Context?

A **distributed system** in the container ecosystem refers to an architecture where workloads are spread across multiple nodes, usually for:

- **Scalability**: Add nodes to increase capacity.
- **Fault Tolerance**: If one node fails, others can take over.
- **Workload Isolation**: Distribute apps across environments for resilience and security.

Containers naturally enable this because they’re **stateless by design**, and platforms like Kubernetes manage orchestration.

> 📘 Intro:
> - [Distributed Systems Primer – Martin Kleppmann (YouTube)](https://www.youtube.com/watch?v=1xo-0gCVhTU)

---

## 2. What is Kubernetes (K8s)?

Kubernetes (k8s) is an **open-source container orchestration platform** that automates the deployment, scaling, and management of containerized applications.
It doesn't include any Ingress, Storage provider, or user management system by default, but it sets the mechanisms for the admin to configure the one they want

### 🔹 Maintainer:
- **Owner**: CNCF (Cloud Native Computing Foundation), originally developed by Google.
- **Repo**: https://github.com/kubernetes/kubernetes
- **Docs**: https://kubernetes.io/docs/

### Key Components

Logical Level:
- API Server (Sets up the communication interface for interacting with K8s)
- etcd (Stores the manifests, configuration and objects created on the Cluster)
- Scheduler (Decides which pod will be scheduled in which node)
- Controller Manager (Monitors and reconcile the cluster with the desired state)
- kubelet (Agent that runs on each Kubernetes node and ensures
that containers are running as specified in their Pod definitions)
- kube-proxy (A network component that runs on each node and manages network rules to route traffic to the appropriate Pods inside the cluster)

Hardware Level:
- Master Node (A node that hosts the Kubernetes control plane components and manages the overall state and scheduling of the cluster)
- Worker Node (A node that runs application Pods and is controlled by the master node; it executes workloads and reports status to the control plane)


---

## 3. What is OpenShift?

**OpenShift** is Red Hat's enterprise distribution of Kubernetes. It adds additional tools and services such as:

- Integrated CI/CD (OpenShift Pipelines)
- Developer Console
- Authentication and OAuth
- Security hardening
- Built-in monitoring/logging

### 🔹 Maintainer:
- **Owner**: Red Hat
- **Repo**: https://github.com/openshift
- **Docs**: https://docs.openshift.com/container-platform/latest/

---

## 4. What is OKD?

**OKD** (Origin Community Distribution of Kubernetes) is the **upstream, community-driven version of OpenShift**. It’s like Fedora is to RHEL.

### 🔹 Maintainer:
- **Owner**: Red Hat / OpenShift Community
- **Repo**: https://github.com/openshift/okd
- **Docs**: https://www.okd.io/

---

## 5. Comparison: Kubernetes vs OpenShift vs OKD

| Feature            | Kubernetes         | OpenShift (OCP)        | OKD (Community)       |
|--------------------|--------------------|------------------------|------------------------|
| Owner              | CNCF               | Red Hat                | Red Hat / Community    |
| License            | Apache 2.0         | Apache 2.0 + Red Hat   | Apache 2.0             |
| Web Console        | Optional (Dashboard) | Included (fully featured) | Included (same as OCP) |
| Container Runtime  | Any CRI (e.g. containerd) | CRI-O only           | CRI-O only             |
| CI/CD Integration  | Optional (ArgoCD, Jenkins) | Built-in (Pipelines) | Built-in              |
| Installation       | Manual, kubeadm    | Assisted (Installer, IPI) | Community tools       |
| Support            | Community          | Enterprise (Paid)      | Community              |

---

## 6. Origins and Evolution

### Ancestors

- **chroot (1979)** → process isolation
- **FreeBSD Jails / Solaris Zones**
- **LXC (2008)** → Linux Containers
- **Apache Mesos (2009)** → Distributed cluster manager that efficiently runs applications and containers at scale.
- **Docker (2013)** → simplified packaging and runtime
- **Kubernetes (2014)** → orchestrator for containers
- **Docker Swarm (2016)** → orchestrator for Docker containers by Docker Inc
- **OpenShift (v3 onwards)** → Kubernetes-based PaaS
- **OpenShift (v4)**  → Improvements in architecture, installation and CRI

---

## 7. Why This Model Became Popular

### ✅ Advantages

- **Dev/prod parity**: Same image runs everywhere
- **Automation**: Deployments, scaling, and healing
- **Speed**: Fast boot and restart times
- **Ecosystem**: Huge CNCF landscape of tools
- **Multicloud-ready**: Cloud-agnostic deployments
- **

### ❌ Disadvantages

- **Complexity**: Steep learning curve
- **Observability**: Requires external tools (Prometheus, Grafana)
- **Stateful workloads**: Still more challenging, but can be adapted
- **Networking**: Often misunderstood and non-trivial

> 📘 Further Reading:
> - [Why Kubernetes is Popular – TechTarget](https://www.techtarget.com/searchitoperations/tip/Why-Kubernetes-gained-popularity-and-why-it-matters)
> - [History of Containers – Red Hat](https://www.redhat.com/en/topics/containers/what-are-linux-containers)

### Other characteristics

- **Self-healing**: Detects and replace failing or unresponsive containers
- **Modular**: Allows extending its functionality by adding Custom Resource Definitions (CRDs)
- **Autoscaling**: Can adjust automatically the amount of containers based on the workload demand
- **Declarative and Imperative**: Supports receiving orders by using manifests (declarative) and by using CLI commands (Imperative)
- **Load Balancing**: Native network LB and service discovery

---

## 🧠 Exercise (Discussion)

**Question:**
What are the main differences between Kubernetes and OpenShift? When would you use each?

**Expected Answer:**
Kubernetes offers flexibility and community-driven innovation, ideal for custom setups. OpenShift offers opinionated defaults, security, and enterprise support, ideal for teams wanting an out-of-the-box platform with guardrails.


## 🧠 Exercise (Discussion)

**Question:**
What are the main differences between Virtual Machines and Containers?

**Expected Answer:**
Containers uses the kernel of the host where they are running and VMs deploys and uses their own Kernel which is totally separated from the hosts' kernel.  Additionally, containers are ephemeral, which means they are stateless, short-lived runtime and their filesystem, process state and data are non-persistent.


## 🧠 Exercise (Research task)
**Question:**
Why Openshift has three master nodes, and not two or four?

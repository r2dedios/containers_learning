# 📘 OpenShift – Day 2: Networking Fundamentals

---

## 🔹 1. Physical Network vs Pod Network vs Service Network

### 🧠 Physical Node Network

Each OpenShift node (control plane or worker) is a **real or virtual machine** connected to a physical network (usually via Ethernet or virtual bridge). This network allows nodes to:

- Communicate with each other (cluster-internal communication)
- Reach the outside world (internet, registries, etc.)
- Expose services to clients

📌 Note: Default physical IP CIDR Block `10.0.3.25`.

---

### 🧠 Pod Network

When Pods are created, they are assigned IPs from a **cluster-wide overlay network** — this is the **Pod Network**.

- Every Pod gets a unique IP (e.g., `10.128.5.34`).
- All Pods can communicate with each other *by default*, even across nodes.
- Pod-to-Pod communication happens over a **Software-Defined Network (SDN)**.
- Each time a Pod is created, a new IP is assigned to it. So IPs are also "ephemeral"

This network is virtual and is managed by a **CNI plugin** (e.g., OVN-Kubernetes).

📌 Note: Default Pod IP CIDR Block `10.128.0.0/14`.

📘 [Pod Networking – Kubernetes Docs](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

---

### 🧠 Service Network

Kubernetes Services expose Pods through **stable virtual IPs** and **DNS names**.

- The service uses `Endpoints` behind the scenes to get the targets
- The `Endpoints` are obtained based on label selectors
- Services act as load balancers for a group of Pods.
- Traffic to a Service is load-balanced to the correct Pods via **iptables**, **IPVS**, or **OVN rules**.
- EVERY Service has a DNS Name `service_name.namespace.svc.cluster.local`

📌 Note: Default Service IP CIDR Block `172.30.0.0/16`.

---

## 🔹 2. What Is a Software-Defined Network (SDN)?

Software-Defined Networking (SDN) is a network architecture that uses software-based controllers or application programming interfaces (APIs) to manage network traffic and resources.
It separates the control plane (where network intelligence and decision-making reside) from the data plane (where traffic is actually forwarded).
This separation allows for more flexible, dynamic, and programmable network control through centralized software. 

In OpenShift:

- SDN is implemented using **OVN-Kubernetes** or legacy **OpenShift SDN**.
- It creates **virtual networks** inside the cluster to handle:
  - Pod-to-Pod routing
  - Pod-to-Service routing
  - Isolation (via Network Policies)

📘 [OVN-Kubernetes Overview](https://docs.openshift.com/container-platform/latest/networking/ovn_kubernetes_network_provider/about-ovn-kubernetes.html)

---

## 🔹 3. Kubernetes Service Types and Port Mapping

| Service Type            | Description                               | Use Case                             | Port Mapping                    |
|-------------------------|-------------------------------------------|--------------------------------------|---------------------------------|
| **ClusterIP** (default) | Internal-only virtual IP for Pods         | App-to-App communication             | Pod IP ← SVC IP ← DNS           |
| **NodePort**            | Exposes a port on every node’s IP         | Access from outside (simple testing) | NodeIP:Port → SVC IP → Pod IP   |
| **LoadBalancer**        | Creates a cloud or external load balancer | Production apps (on cloud)           | LB IP → NodePort → SVC IP → Pod |
| **ExternalName**        | Maps a service to a DNS name              | Legacy system integration            | DNS alias only                  |

📘 [Services – Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/)

---

## 🔹 4. Endpoints and Label Selectors

### 🧠 Endpoints

A `Service` does not directly point to Pods. Instead, it points to **Endpoints**, which are IP + Port combinations derived from selected Pods.

- Endpoints are automatically updated as Pods are created/deleted.
- A Service without Endpoints cannot forward traffic.

Check with: `oc get endpoints <service-name>`

---

### 🧠 Label Selectors

Services use **label selectors** to choose which Pods to route traffic to.

Example:

```yaml
selector:
  app: frontend
```

All Pods with `app=frontend` will be targeted.

---

## 🧠 Exercise (Question)
**Question**
You have a Pod that responds correctly when you curl its IP from another Pod, but the Service pointing to it shows no response. What could be wrong?

## 🧠 Exercise (Question)
**Question**
You created a ClusterIP service and your app works inside the cluster, but it’s not reachable from your laptop. Why?

Is it possible to access to it from my laptop in any other way? 

## 🧠 Exercise (Question)
**Question**
You created a Service and it shows a ClusterIP, but no traffic flows. You check and there are no Pods running yet. Why does the Service still exist?

## 🧠 Exercise (Question)
**Question**
Is it possible to access to a Service in a different Namespace?
Is it possible to create a Service, referring to pods on a different Namespace? Why?

## 🧠 Exercise (Hands-on task)
Apply this manifest:
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: day-2-deployment
  labels:
    lesson: day-2
spec:
  replicas: 3
  selector:
    matchLabels:
      app: http-echo
      lesson: day-2
      purpose: testing
      version: v1
  template:
    metadata:
      name: learning-pod
      labels:
        app: http-echo
        lesson: day-2
        purpose: testing
        version: v1
    spec:
      restartPolicy: Always
      containers:
        - name: ip-echo
          image: busybox:latest
          command: ["sh", "-c"]
          args:
            - |
              while true; do
                echo -e "HTTP/1.1 200 OK\\n\\n Hello, I'm $(hostname -i)" | nc -l -p 8080;
              done
          ports:
          - containerPort: 8080
```

Create a ClusterIP Service for accessing the entire set of replicas on port 1234
What would happen if you GET this Service multiple times? Who will respond?

To check if your service is correct, run this command:
```
wget http://day-2-service:1234 -O - -q
```

## 🧠 Exercise (Hands-on task)
Create a Service called `genius` for accessing "google.com" using a internal Service IP.
Run this command in any `day-2-deployment` pod for testing
```sh
ping genius
```

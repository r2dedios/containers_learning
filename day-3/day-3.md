# 📘 OpenShift – Day 3: Networking Advanced (Ingress and Egress)

## 🔹 1. Exposing Services to the Outside World in Kubernetes & OpenShift

In Day-2 lesson, it was explained that the SDN is isolated from the Hardware
network, so.. How are the pods running on the cluster exposed to allow clients to
connect to them?

Kubernetes and OpenShift offer multiple mechanisms to expose
workloads running inside the cluster to clients outside the cluster. Each method
serves different use cases, levels of control, and flexibility.

---

### ✅ NodePort Service

- First, and easiest way for exposing services
- Exposes a `Service` on a static port on every node in the cluster.
- The service is reachable via `<NodeIP>:<NodePort>`.
- Basic and easy to use but **not production-grade** for most setups.

**Use case:**
Quick testing, dev clusters without load balancers.

📘 [NodePort – Kubernetes Docs](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport)

---

### ✅ LoadBalancer Service

- More flexible and powerful approach, but requires external IP and LB provider.
- Integrates with cloud providers to provision an external Load Balancer automatically.
- The external LB forwards traffic to a `Service` of type `NodePort`.
- Provides a stable IP for external access.
- Can be used in BareMetal, but replacing the public cloud provider by another provider (like [MetalLB](https://metallb.io/))

**Use case:**
Cloud-native environments with native load balancer integrations.

📘 [LoadBalancer – Kubernetes Docs](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)

---

### ✅ Ingress (Kubernetes)

- Similar to LoadBalancer, but without third party dependencies
- API object for **HTTP/HTTPS routing**.
- Requires an **Ingress Controller** (e.g., NGINX, HAProxy, Traefik).
- Allows **host-based and path-based** routing (Requires Layer 7)

**Use case:**
Centralized routing for HTTP traffic with TLS support.

📘 [Ingress – Kubernetes Docs](https://kubernetes.io/docs/concepts/services-networking/ingress/)

---

### ✅ Route (OpenShift)

- OpenShift-specific object similar to Ingress, but integrated with the **HAProxy router**.
- Maps a `Service` to a publicly accessible DNS name.
- Supports TLS termination, re-encryption, path routing.

**Use case:**
Standard way to expose HTTP(S) workloads in OpenShift.

📘 [Routes – OpenShift Docs](https://docs.redhat.com/en/documentation/openshift_container_platform/4.18/html/ingress_and_load_balancing/configuring-routes)

---

### ✅ Gateway API

- Next-generation alternative to Ingress, offering more extensibility and protocol support.
- Separates **infrastructure (Gateway)** from **traffic policies (Routes)**.
- Supports HTTP, TCP, TLS, gRPC via specific Route types.

**Use case:**
Multi-protocol routing, multi-team environments, advanced L4/L7 control.

📘 [Gateway API – Kubernetes](https://gateway-api.sigs.k8s.io/)

---

## 🔹 2. What is Ingress in Kubernetes?

Ingress is a Kubernetes API object that **manages external access to Services**,
typically HTTP/S. It defines routing rules to direct traffic from outside the
cluster to internal Services.

**Key Concepts:**
- Requires an **Ingress Controller** to function.
- Supports **host-based** and **path-based** routing.
- Works at **Layer 7 (HTTP)** of the OSI model.

📘 [Ingress – Kubernetes Docs](https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/ingress_and_load_balancing/configuring-ingress-cluster-traffic)

---

## 🔹 3. Ingress Architecture in Kubernetes

- Client sends an HTTP request to the **Ingress Controller**.
- Controller inspects the request and matches against Ingress rules.
- If matched, traffic is routed to the corresponding `Service` → `Pod`.

**Typical Ingress flow:**

    Client ──▶ Ingress Controller ──▶ Service ──▶ Pod


### Ingress controllers comparison
| Project     | Technology   | Features                             | Notes                        |
| ----------- | ------------ | ------------------------------------ | ---------------------------- |
| **NGINX**   | L7 Proxy     | TLS, path/host routing               | Most widely used, flexible   |
| **HAProxy** | L7 Proxy     | TCP/HTTP routing, TLS passthrough    | Used by OpenShift            |
| **Traefik** | L7 Proxy     | Dynamic config, metrics              | Simple and cloud-native      |
| **Istio**   | Service Mesh | Traffic shaping, security, telemetry | Heavyweight, advanced        |
| **Contour** | Envoy-based  | CRDs, TLS offloading                 | Lightweight and Envoy-native |

---

## 🔹 4. What is Egress in Kubernetes?

**Egress** refers to outbound traffic **from inside the cluster to external systems** (e.g., internet, databases, APIs).

**Egress Management Techniques:**
- **Egress IPs**: Assign static IPs for outbound traffic.
- **Network Policies**: Restrict Pod-to-external access.
- **Egress Gateways** (in service meshes): Centralize and secure outbound traffic.

📘 [Egress Traffic – Kubernetes](https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/ovn-kubernetes_network_plugin/configuring-egress-traffic-loadbalancer-services)

---

## 🔹 5. Ingress and Egress in OpenShift

### 🧠 Ingress in OpenShift

- OpenShift does **not use `Ingress` objects by default**. It already has its built-in Ingress (HAProxy)
- Instead, it uses **Routes**, a native OpenShift resource.
- A Route maps an OpenShift `Service` to an external DNS hostname via the **HAProxy router**.

**Example Route:**

```bash
oc expose svc myapp --hostname=myapp.apps.cluster.example.com
```
📘 [Routes - Openshift Docs](https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/ingress_and_load_balancing/configuring-routes)


### 🧠 Egress in OpenShift
OVN-Kubernetes support advanced egress features:
- Egress IPs: Assign a static IP to a Pod or namespace.
- Egress Firewalls: Block or allow traffic based on destination IPs or CIDRs.

📘 [Egress IPs – OpenShift Docs](https://docs.openshift.com/container-platform/latest/networking/openshift_sdn/assigning-egress-ips.html)

---

## 🔹 7. What Is a Gateway in Kubernetes?
Gateway is a new standard from the Gateway API project that aims to replace the traditional Ingress with a more flexible, extensible model for traffic routing.

### 🔧 Key Improvements:
- Decouples routing configuration from infrastructure provisioning.
- Supports multiple protocols (HTTP, TCP, TLS, gRPC).
- Uses GatewayClasses and Routes as modular building blocks.
- Better suited for multi-team, multi-tenant environments.

📘 Gateway API – Kubernetes


## 🧠 Exercise (Question)
**Question**
If the Ingress is implemented by HAproxy? Who implements the Egress?

## 🧠 Exercise (Question)
**Question**
If we need an Ingress for exposing services, and the Ingress is running in the cluster too, how we expose the Ingress?

## 🧠 Exercise (Hands-on task)
Apply this manifest:
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: day-3-deployment
  labels:
    lesson: day-3
spec:
  replicas: 1
  selector:
    matchLabels:
      app: http-echo
      lesson: day-3
      purpose: testing
      version: v1
  template:
    metadata:
      name: learning-pod
      labels:
        app: http-echo
        lesson: day-3
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

Now, expose this deployment to be able to access it from **OUTSIDE** of the cluster:
* NodePort service. Does it work in Openshift? How can you access to it?
* LoadBalancer service.
* Route: The route **MUST** be configured with TLS, and redirect the HTTP requests to HTTPS

## 🔹 EXTRA: Internal DNS

Every Kubernetes/OpenShift cluster has an **internal DNS service** (usually `CoreDNS`).

- Services get a name like `my-service.my-namespace.svc.cluster.local`.
- Pods use this DNS to resolve service names.
- Works without needing to hard-code IPs.

Test with: `nslookup my-service` inside a Pod.

📘 [DNS for Services and Pods – Kubernetes](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)

---

## 🧠 Exercise (Question)
**Question**
If we expose a TCP service (PGSQL) by an Openshift Route, will it work? If so, explain how

## 🧠 Exercise (Hands-on task)
Apply this manifests
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: day-3-A
  labels:
    lesson: day-3
    app: A
spec:
  replicas: 1
  selector:
    matchLabels:
      app: A
      lesson: day-3
      purpose: testing
      version: v1
  template:
    metadata:
      name: learning-pod
      labels:
        app: A
        lesson: day-3
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
                echo -e "HTTP/1.1 200 OK\\n\\n Hello, I'm Deployment A: $(hostname -i)" | nc -l -p 8080;
              done
          ports:
          - containerPort: 8080
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: day-3-B
  labels:
    lesson: day-3
    app: B
spec:
  replicas: 1
  selector:
    matchLabels:
      app: B
      lesson: day-3
      purpose: testing
      version: v1
  template:
    metadata:
      name: learning-pod
      labels:
        app: B
        lesson: day-3
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
                echo -e "HTTP/1.1 200 OK\\n\\n Hello, I'm Deployment B: $(hostname -i)" | nc -l -p 8080;
              done
          ports:
          - containerPort: 8080
```
**Question**
- Expose Both services on the same route, but with different paths `/app-A` and
`/app-B`. In other words, If the Route has the hostname
`multipath-route.apps.cluster.base.domain` the route should work like:
```sh
curl -XGET https://multipath-route.apps.cluster.base.domain/app-A
> Hello, I'm Deployment A: day-3-a-.....
```
```sh
curl -XGET https://multipath-route.apps.cluster.base.domain/app-B
> Hello, I'm Deployment B: day-3-b-.....
```


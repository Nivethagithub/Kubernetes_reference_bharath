**Kubernetes Ingress:**

**Introduction**

**Kubernetes Ingress** is a crucial concept in **container orchestration**, helping manage external access to services within a Kubernetes cluster. Many engineers find Ingress **complex** due to two primary reasons:

**1.	Understanding the need for Ingress** – If the purpose of Ingress is unclear, the concept can seem confusing.

**2.	Practical implementation issues** – Engineers struggle to set up Ingress on **local clusters (Minikube, Kind)** due to misconfigurations.

This guide explains **why Ingress is needed, how it solves key problems in Kubernetes networking, and how to implement it effectively**. We'll also cover **real-world use cases, best practices, and common pitfalls**.

---

**Why Do We Need Ingress?**

Before Kubernetes **v1.1 (2015)**, Kubernetes did not have Ingress. Applications were exposed externally using **Kubernetes Services** in the following ways:

•	**ClusterIP** – Internal access only (not exposed externally).

•	**NodePort** – Exposes the service on each node's IP and a static port.

•	**LoadBalancer** – Uses cloud provider's load balancer to expose the service externally.

**The Problems with Kubernetes Services**

While **Kubernetes Services** help with **load balancing and service discovery**, they have limitations:

**1.	Limited Load Balancing Capabilities**

o	Kubernetes **Services** only provide **round-robin** load balancing.

o	They **lack advanced load balancing features** such as:

▶ **Path-based routing** (`/app1 → Service A`, `/app2 → Service B`)

▶ **Host-based routing** (`app.example.com → Service A`, `api.example.com → Service B`)

▶ **Sticky sessions** (session persistence for users)

▶ **TLS termination** (secure HTTPS traffic handling)
  
**2.	High Cloud Costs for Load Balancers**

o	Each **Service of type LoadBalancer** gets a **separate cloud-managed load balancer** (AWS, Azure, GCP).

o	Large enterprises like **Amazon, Netflix, Uber** run **thousands of microservices**.

o	This leads to **excessive billing** due to multiple **static IP addresses** and load balancer charges.

**How Ingress Solves These Problems**

**Kubernetes Ingress solves these issues by providing:**

•	**Path-based Routing** → Route traffic based on URL paths (e.g., /api → Service A, /web → Service B).

•	**Host-based Routing** → Route traffic based on domains (e.g., app.example.com → Service A).

•	**TLS Termination** → Offload HTTPS encryption/decryption.

•	**Single Load Balancer** → Uses **one external IP** instead of multiple LoadBalancer services.

•	**Custom Load Balancer Rules** → Supports **NGINX, HAProxy, Traefik, F5**, and more.

---

**How Ingress Works**

Kubernetes **Ingress** introduces two components:

**1.	Ingress Resource**

o	A YAML configuration defining routing rules.

o	Specifies **path-based, host-based routing, TLS, and backend services**.

**2.	Ingress Controller**

o	A **Kubernetes component that processes Ingress resources.**

o	Implements the actual routing using **NGINX, Traefik, HAProxy, etc**.

o	Examples:

  **NGINX Ingress Controller**
  
  **Traefik Ingress**
  
  **HAProxy Ingress**
  
**Ingress Architecture**

```sh
           Client Request
                 |
                 v
    +---------------------------+
    |  Ingress Controller (NGINX) |
    +---------------------------+
           |         |
           |         |
 +---------+         +---------+
 |  Service A        |  Service B
 |  (/app1)         |  (/app2)
 +---------+         +---------+
```

---

**Installing and Configuring Ingress**

**Step 1: Install an Ingress Controller**

For **Minikube:**

```sh
minikube addons enable ingress
```

For **NGINX Ingress on EKS, GKE, AKS** (Production Setup):

```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install my-ingress-controller ingress-nginx/ingress-nginx
```

Verify installation:

```sh
kubectl get pods -n ingress-nginx
```

---

**Step 2: Create an Ingress Resource**

Save the following YAML as ingress.yaml:

```sh
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: service-a
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: service-b
            port:
              number: 80
```

Apply the Ingress:

```sh
kubectl apply -f ingress.yaml
```

Verify Ingress:

```sh
kubectl get ingress
```

Output:

```sh
NAME              CLASS    HOSTS        ADDRESS        PORTS   AGE
example-ingress   <none>   foo.bar.com  192.168.1.100  80      5m
```

---

**Step 3: Testing Ingress Locally**

To access the Ingress, modify your **/etc/hosts** file:

```sh
192.168.1.100  foo.bar.com
```

Now, test with curl:

```sh
curl http://foo.bar.com/app1
curl http://foo.bar.com/app2
```

---

**Best Practices for Using Kubernetes Ingress**

**1.	Choose the Right Ingress Controller**

o	**NGINX Ingress** (most common, lightweight).

o	**Traefik** (best for Kubernetes-native applications).

o	**HAProxy, AWS ALB, GKE Ingress** for cloud-native deployments.

**.	Use TLS/HTTPS for Secure Ingress**

o	Encrypt traffic using **Let’s Encrypt + cert-manager**.

o	Example TLS Ingress:

```sh
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-ingress
spec:
  tls:
  - hosts:
    - secure.example.com
    secretName: tls-secret
  rules:
  - host: secure.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: secure-service
            port:
              number: 443
```
 
**3.	Avoid Multiple LoadBalancers**

o	Use **one external LoadBalancer with Ingress** instead of multiple LoadBalancer-type services.

**4.	Monitor Ingress Performance**

o	Enable **Prometheus & Grafana** for Ingress monitoring.

o	Use kubectl logs -n ingress-nginx <ingress-pod> for debugging.

---

**Conclusion**

Kubernetes **Ingress** simplifies external access to applications running inside Kubernetes clusters. It **reduces cloud costs, enhances security, and provides enterprise-level load balancing capabilities**.

By properly setting up **Ingress Controllers, TLS, and routing rules**, organizations can achieve **scalable, secure, and cost-effective traffic management** for Kubernetes workloads.

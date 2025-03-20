**Kubernetes Services Deep Dive:**

**Introduction**

Kubernetes Services play a crucial role in managing network traffic between pods and exposing applications within and outside a Kubernetes cluster. Without services, direct communication between pods would be unreliable due to dynamic IP allocation.

In this guide, we will **deep dive into Kubernetes Services**, focusing on three key aspects:

**1.	Load Balancing** – Distributing traffic across multiple pods.

**2.	Service Discovery** – Enabling efficient communication between components.

**3.	Application Exposure** – Exposing applications within a cluster and to the external world.

We will also **use practical examples and tools like kubectl and CubeShark to visualize traffic flow within Kubernetes.**

---

**Understanding Kubernetes Services**

**Why Do We Need Kubernetes Services?**

By default, each pod in Kubernetes gets a unique **IP address**, but this IP is not persistent. If a pod dies and a new one replaces it, the IP changes. This makes direct communication unreliable.

**Kubernetes Services** provide:

**•	Stable networking** by using labels and selectors instead of dynamic pod IPs.

**•	Load balancing** across multiple pod instances.

**•	External access** through NodePort and LoadBalancer services.

---

**Setting Up a Kubernetes Cluster**

For this demo, we will use **Minikube**, but the same concepts apply to AWS, Azure, and GCP.

**Step 1: Start Minikube**

```sh
minikube start
```

Check if the cluster is running:

```sh
kubectl get nodes
```

Expected output:

```sh
NAME       STATUS    ROLES    AGE    VERSION
minikube   Ready     master   10m    v1.28.0
```

---

**Deploying an Application on Kubernetes**

**Step 2: Create a Deployment**

Let's create a sample **Python-based Django application** as a Kubernetes deployment.

**Deployment YAML (deployment.yaml)**

```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-python-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-python-app
  template:
    metadata:
      labels:
        app: sample-python-app
    spec:
      containers:
        - name: python-app
          image: python:3.9
          ports:
            - containerPort: 8000
```

Apply the deployment:

```sh
kubectl apply -f deployment.yaml
```

Check the running pods:

```sh
kubectl get pods
```

---

**Understanding Kubernetes Service Types**

**1. ClusterIP (Default Service)**

•	The default type of Kubernetes Service.

•	Exposes the service internally within the cluster.

•	Cannot be accessed from outside the cluster.

**ClusterIP YAML (service-clusterip.yaml)**

```sh
apiVersion: v1
kind: Service
metadata:
  name: python-app-service
spec:
  selector:
    app: sample-python-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
```

Apply the service:

```sh
kubectl apply -f service-clusterip.yaml
```

Check service details:

```sh
kubectl get svc
```

Output:

```sh
NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)
python-app-service     ClusterIP   10.98.211.124    <none>        80/TCP
```

 ❌ This service is **not accessible outside the cluster**.

---

**2. NodePort Service (Exposing Within Organization)**

•	Exposes the service on a **node's IP address and a static port**.

•	Useful for internal access within an organization's network.

•	Not ideal for production since it lacks a managed load balancer.

**NodePort YAML (service-nodeport.yaml)**

```sh
apiVersion: v1
kind: Service
metadata:
  name: python-app-service
spec:
  type: NodePort
  selector:
    app: sample-python-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
      nodePort: 30007
```

Apply the service:

```sh
kubectl apply -f service-nodeport.yaml
```

Check the service:

```sh
kubectl get svc
```

Output:

```sh
NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)
python-app-service     NodePort    10.98.211.124    <none>        80:30007/TCP
```

**Accessing the Application:**

```sh
minikube service python-app-service --url
```

or manually:

```sh
curl http://$(minikube ip):30007
```

 ✅ **Now, the application is accessible within the organization via the node's IP and assigned port (30007)**.

 ---
 
**3. LoadBalancer Service (Exposing to the Public)**

•	**Recommended for production** in cloud environments.

•	Automatically provisions an **external load balancer**.

•	Assigns a **public IP address**.

**LoadBalancer YAML (service-loadbalancer.yaml)**

```sh
apiVersion: v1
kind: Service
metadata:
  name: python-app-service
spec:
  type: LoadBalancer
  selector:
    app: sample-python-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
```

Apply the service:

```sh
kubectl apply -f service-loadbalancer.yaml
```

Check for an external IP:

```sh
kubectl get svc
```

Output (on AWS, GCP, Azure):

```sh
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)
python-app-service     LoadBalancer   10.98.211.124    34.125.23.45   80/TCP
```

Accessing the Application:

```sh
curl http://34.125.23.45
```

 ✅ **Now, the application is publicly accessible using a public IP address**.

---

**Visualizing Kubernetes Traffic Flow with CubeShark**

To understand **how Kubernetes Services distribute traffic**, we use **CubeShark**.

**Installing CubeShark:**

```sh
curl -fsSL https://kubeshark.co/install | bash
```

Start CubeShark:

```sh
kubeshark tap -A
```

Access the CubeShark dashboard:

```sh
http://localhost:8899
```

**Load Balancing Visualization**

To test Kubernetes Service **load balancing**, send multiple requests:

```sh
for i in {1..6}; do curl -s http://$(minikube ip):30007; done
```

In CubeShark, observe the **round-robin traffic distribution:**

```sh
Request 1 → Pod 1
Request 2 → Pod 2
Request 3 → Pod 1
Request 4 → Pod 2
```

 ✅ **Traffic is distributed evenly across pods, confirming load balancing**.

---

**Conclusion**

In this guide, we covered:

✔ **ClusterIP Services** – Internal access within a Kubernetes cluster.

✔ **NodePort Services** – Exposes services within an organization's network.

✔ **LoadBalancer Services** – Makes applications publicly accessible.

✔ **Service Discovery** – How services find pods dynamically using labels and selectors.

✔ **Load Balancing** – Distributing traffic across multiple pods.

✔ **Traffic Flow Analysis with CubeShark** – Debugging network traffic visually.

**Next Steps**

•	**Try implementing these services in AWS, Azure, or GCP.**

•	**Use CubeShark for deeper insights into Kubernetes networking.**

•	**Explore advanced Kubernetes networking with Ingress controllers.**

---

📌 **If you found this useful, star ⭐ this GitHub repository and contribute with suggestions or improvements.**

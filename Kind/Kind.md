**Kind**

**What is Kind (Kubernetes in Docker)?**

**Kind (Kubernetes in Docker)** is an open-source tool designed for running **Kubernetes clusters** in **Docker containers**. It was developed primarily for **testing Kubernetes itself** but is now widely used by developers and DevOps engineers for local development, testing, and CI/CD pipeline integration.

---

**Why Use Kind?**

1.	**Lightweight**: Runs Kubernetes clusters inside Docker, avoiding the need for VMs or cloud resources.
  
2.	**Fast Cluster Creation**: Spins up clusters quickly compared to minikube or kubeadm.
  
3.	**Supports Multi-Node Clusters:** Can create multi-node Kubernetes clusters for testing.
   
4.	**Used in CI/CD Pipelines:** Since it runs in Docker, it is perfect for GitHub Actions, GitLab CI/CD, or Jenkins 
      pipelines.
  	
5.	**No Need for Hypervisors:** Unlike Minikube, Kind doesn’t require VirtualBox, KVM, or other hypervisors.

---

**How Kind Works Internally**

Kind runs Kubernetes clusters as **Docker containers**. Each cluster node is a Docker container running:

•	A **containerized Kubernetes control plane and worker nodes.**

•	A **custom network** for internal communication.

•	A **Docker-in-Docker (DinD) setup** to simulate multiple nodes.

When you create a Kind cluster, it:

1.	**Pulls the Kind base image** (which includes Kubernetes components).
  
2.	**Creates a Docker network** for Kubernetes communication.
   
3.	**Launches control-plane and worker nodes as Docker containers.**
  
4.	**Bootstraps Kubernetes** using kubeadm inside these containers.
  
5.	**Exposes the Kubernetes API** for interaction.

---

**How to Install Kind and Create a Local Kubernetes Cluster**

**Step 1: Install Prerequisites**

Before installing Kind, you need:

1.	**Docker** (since Kind runs Kubernetes clusters in Docker)
  
2.	**Kubectl** (to interact with the Kubernetes cluster)

**Install Docker**


For Ubuntu/Linux:

```bash
sudo apt update
sudo apt install docker.io -y
```

For Mac:

```bash
brew install --cask docker
```

For Windows:

•	Install Docker Desktop from Docker's official site.

**Install Kubectl**

For Linux:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

For Mac:

```bash
brew install kubectl
```

For Windows:

```bash
choco install kubernetes-cli
```

---

**Step 2: Install Kind**

For Linux & Mac:

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x kind
sudo mv kind /usr/local/bin/
```

For Mac (using Homebrew):

```bash
brew install kind
```

For Windows:

```bash
choco install kind
```

---

**Step 3: Create a Kubernetes Cluster with Kind**

To create a default single-node cluster:

```bash
kind create cluster --name my-cluster
```

This will:

•	Pull the latest Kubernetes image.

•	Create a single-node Kubernetes cluster.

To verify:

```bash
kubectl cluster-info --context kind-my-cluster
kubectl get nodes
```

---

**Step 4: Create a Multi-Node Cluster**

If you need a **multi-node cluster** (1 control plane, 2 workers):

1.	Create a configuration file (kind-config.yaml):

```bash
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

1.	Deploy the cluster:

```bash
kind create cluster --name multi-node-cluster --config kind-config.yaml
```

1.	Verify the nodes:

```bash
kubectl get nodes
```

---

**Step 5: Exposing Ports (LoadBalancer / Ingress)**

By default, Kind does not support **LoadBalancer Services** like cloud-based Kubernetes. You can use **NodePort** or an **Ingress Controller.**

**Expose a Port Using NodePort**

```bash
kubectl expose deployment my-app --type=NodePort --port=80
```

Find the port:

```bash
kubectl get svc
```

Then access the service:

```bash
curl http://localhost:<NodePort>
```

**Using Ingress**

If your application requires an **Ingress Controller**, install nginx-ingress:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

---

**Step 6: Delete a Cluster**

To delete a cluster:

```bash
kind delete cluster --name my-cluster
```

To delete all clusters:

```bash
kind delete clusters --all
```

---

**When to Use Kind vs. Other Local Kubernetes Tools?**

1.	**Kind** is best for running Kubernetes clusters in **Docker**. It is lightweight and mainly used for **CI/CD 
      pipelines, testing, and local development**. However, it does not support LoadBalancer services by default, so you 
      need an **Ingress Controller** for external access.
  
2.	**Minikube** is better for **local development** with full Kubernetes features, including LoadBalancer support. 
      However, it requires a **hypervisor** (like VirtualBox) and is heavier compared to Kind.
  
3.	**K3s** is a lightweight Kubernetes distribution designed for **edge computing and IoT devices**. It is optimized for 
      low-resource environments while still supporting most Kubernetes features.
   
4.	**MicroK8s** is a minimal Kubernetes distribution from Canonical, ideal for **cloud and local development**. It 
      supports high availability and cloud integration, making it a good choice for small-scale production environments.
  	
If you need **fast, lightweight, and CI/CD-friendly Kubernetes clusters**, Kind is the best choice. If you want a full Kubernetes experience for **local development**, Minikube is a better option. For **edge devices or minimal resource environments**, K3s or MicroK8s are recommended.

---

•	**Kind is a lightweight, Docker-based Kubernetes tool** for local development and CI/CD.

•	**Installation is simple**, requiring only Docker and kind.

•	**Multi-node clusters are easy to set up** using configuration files.

•	**Lacks LoadBalancer support**, but Ingress controllers can be used.

•	**Perfect for CI/CD pipelines**, GitHub Actions, GitLab CI/CD, or Jenkins.

**Real-World DevOps Project Using Kind (Kubernetes in Docker)**

This hands-on project will **deploy a microservice-based application** on a **multi-node Kubernetes cluster** using **Kind, Docker, and GitLab CI/CD.**

---

**Project Overview**

**Objective**

Deploy a **Node.js + MongoDB microservice application on a Kind Kubernetes cluster** with the following:

•	**Dockerized application** (Node.js backend + MongoDB database)

•	**Helm chart for deployment**

•	**GitLab CI/CD pipeline** to automate deployment

**Tools Used**

•	**Kind** (For Kubernetes Cluster)

•	**Docker** (For containerization)

•	**Helm** (For Kubernetes deployment)

•	**GitLab CI/CD** (For automation)

•	**kubectl** (For managing the cluster)

•	**Ingress Controller** (For exposing the service)

---

**Step 1: Set Up a Multi-Node Kind Cluster**

Create a **Kind cluster configuration file** (kind-cluster.yaml):

```bash
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

Create the cluster:

```bash
kind create cluster --name devops-project --config kind-cluster.yaml
```

Verify:

```bash
kubectl get nodes
```

---

**Step 2: Create a Node.js + MongoDB App**

**Backend (Node.js API)**

Create server.js:

```bash
const express = require('express');
const mongoose = require('mongoose');

const app = express();
const port = process.env.PORT || 3000;
const dbUrl = process.env.MONGO_URL || "mongodb://mongo:27017/devopsdb";

mongoose.connect(dbUrl, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log("MongoDB Connected"))
    .catch(err => console.log(err));

app.get("/", (req, res) => {
    res.send("Hello from DevOps project running on Kubernetes!");
});

app.listen(port, () => console.log(`Server running on port ${port}`));
```

Create Dockerfile:

```bash
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "server.js"]
```

---

**Step 3: Write Kubernetes Manifests**

**Deployment & Service for Node.js App**


Create app-deployment.yaml:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
        - name: node-app
          image: mydockerhub/node-app:latest
          ports:
            - containerPort: 3000
          env:
            - name: MONGO_URL
              value: "mongodb://mongo:27017/devopsdb"
---
apiVersion: v1
kind: Service
metadata:
  name: node-service
spec:
  selector:
    app: node-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: ClusterIP
```

**MongoDB Deployment & Service**

```bash
Create mongo-deployment.yaml:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo
          ports:
            - containerPort: 27017
---
apiVersion: v1
kind: Service
metadata:
  name: mongo
spec:
  selector:
    app: mongo
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```

---

**Step 4: Apply Kubernetes Manifests**

Apply configurations:

```bash
kubectl apply -f mongo-deployment.yaml
kubectl apply -f app-deployment.yaml
```

Check:

```bash
kubectl get pods
kubectl get services
```

---

**Step 5: Expose the App via Ingress**

Kind does not support LoadBalancer, so we use **Ingress.**

**Install Nginx Ingress Controller**

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

Create ingress.yaml:

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
    - host: devops.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: node-service
                port:
                  number: 80
```

Apply:

```bash
kubectl apply -f ingress.yaml
```

Edit /etc/hosts (Linux/Mac) or C:\Windows\System32\drivers\etc\hosts (Windows):

```bash
127.0.0.1 devops.local
```

Now, visit http://devops.local.

---

**Step 6: Automate Deployment with GitLab CI/CD**

In .gitlab-ci.yml:

```bash
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - docker build -t mydockerhub/node-app:latest .
    - docker login -u "$DOCKER_USER" -p "$DOCKER_PASSWORD"
    - docker push mydockerhub/node-app:latest

deploy:
  stage: deploy
  script:
    - kubectl apply -f mongo-deployment.yaml
    - kubectl apply -f app-deployment.yaml
    - kubectl apply -f ingress.yaml
```

---

**Final Testing**

```bash
curl http://devops.local
```

Output:

```bash
Hello from DevOps project running on Kubernetes!
```

---

**Summary**

•	**Set up a multi-node Kind cluster**

•	**Deployed a Node.js + MongoDB app**

•	**Used Helm and Ingress for management**

•	**Automated deployment with GitLab CI/CD**

**Kustomize in Kubernetes**

Kustomize is a built-in Kubernetes tool that allows you to customize Kubernetes YAML configurations without modifying the original YAML files. It helps manage environment-specific configurations, overlays, and reusable components.

---

**1. Basics of Kustomize**

Kustomize is a **template-free** way to customize Kubernetes manifests. Unlike Helm, which uses Go templates, Kustomize uses **patching** to modify configurations.

**Key Features:**

•	**Declarative management**: Uses kustomization.yaml files.

•	**Base and Overlay**: Defines a **base configuration** and modifies it using **overlays**.

•	**Reusability**: Keeps YAML files DRY (Don’t Repeat Yourself).

•	**Built-in in** kubectl: Since Kubernetes v1.14, Kustomize is integrated into kubectl apply -k.

**Installation**

If you are using kubectl (v1.14+), Kustomize is built-in:

```bash
kubectl apply -k .
```

To install it separately:

```bash
brew install kustomize  # MacOS
```

---

**2. Kustomize Components**

Kustomize works with a directory structure containing:

•	kustomization.yaml (Main Kustomize configuration)

•	base/ (Base Kubernetes manifests)

•	overlays/ (Environment-specific configurations)

**Example Directory Structure**

```bash
kustomize-app/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── kustomization.yaml
├── overlays/
│   ├── dev/
│   │   ├── kustomization.yaml
│   │   ├── patch-deployment.yaml
│   ├── prod/
│   │   ├── kustomization.yaml
│   │   ├── patch-deployment.yaml
```

---

**3. Base Configuration**

A **base** is a reusable Kubernetes manifest that serves as the foundation.

**Base** deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx:latest
        ports:
        - containerPort: 80
```

**Base** service.yaml

```bash
apiVersion: v1
kind: Service
metadata:
  name: my-app
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

**Base** kustomization.yaml

```bash
resources:
- deployment.yaml
- service.yaml
```

---

**4. Patching in Kustomize**

Patching allows modifying **specific parts** of the base without duplicating files.

**Example: Overriding Replicas in Dev Environment**

overlays/dev/patch-deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2  # Override base value
```

overlays/dev/kustomization.yaml

```bash
resources:
- ../../base

patches:
- path: patch-deployment.yaml
```

Apply the patch:

```bash
kubectl apply -k overlays/dev
```

---

**5. ConfigMaps and Secrets in Kustomize**

Kustomize **generates ConfigMaps and Secrets** without storing sensitive data in YAML.

**ConfigMap Generator**

kustomization.yaml

```bash
configMapGenerator:
- name: my-config
  literals:
    - APP_ENV=development
    - DEBUG=true
```

Generated output:

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config-abc123
data:
  APP_ENV: development
  DEBUG: "true"
```

**Secret Generator**

kustomization.yaml

```bash
secretGenerator:
- name: my-secret
  literals:
    - DB_PASSWORD=mysecurepassword
    - API_KEY=12345
```

**Applying ConfigMaps & Secrets**

```bash
kubectl apply -k .
```

---

**6. Advantages of Kustomize**

✅ **No need for Helm** – Works natively with Kubernetes.

✅ **Keeps YAML DRY** – Avoids duplication across environments.

✅ **Patch-based customization** – Modify only what's necessary.

✅ **Integrated with kubectl** – No extra tools needed.

---

**Conclusion**

Kustomize is a powerful Kubernetes-native tool for **managing multiple environments** efficiently. It helps DevOps engineers keep Kubernetes manifests clean, modular, and reusable.

**7. Hands-on Guide: Deploying a Microservices-Based E-Commerce Application using Kustomize**

In this guide, we will deploy a **microservices-based e-commerce application** using **Kustomize** in Kubernetes. The project will include:

•	**Multiple microservices (e.g., Payment, Order, User)**

•	**Secrets management using Kustomize**

•	**Ingress configuration for external access**

•	**Environment-specific configurations (Dev & Prod)**

---

**1. Project Structure**

We will organize our files into:

•	base/ → Contains common Kubernetes configurations.

•	overlays/dev/ → Dev-specific configurations.

•	overlays/prod/ → Prod-specific configurations.

```bash
ecommerce-kustomize/
├── base/
│   ├── deployment-payment.yaml
│   ├── deployment-order.yaml
│   ├── service-payment.yaml
│   ├── service-order.yaml
│   ├── ingress.yaml
│   ├── kustomization.yaml
├── overlays/
│   ├── dev/
│   │   ├── kustomization.yaml
│   │   ├── patch-deployment.yaml
│   │   ├── configmap.yaml
│   │   ├── secrets.yaml
│   ├── prod/
│   │   ├── kustomization.yaml
│   │   ├── patch-deployment.yaml
│   │   ├── configmap.yaml
│   │   ├── secrets.yaml
```

---

**2. Base Configuration**

The base contains the core Kubernetes manifests.

**Base Payment Service Deployment** (base/deployment-payment.yaml)

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payment-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: payment-service
  template:
    metadata:
      labels:
        app: payment-service
    spec:
      containers:
      - name: payment-service
        image: myrepo/payment-service:latest
        envFrom:
        - configMapRef:
            name: payment-config
        - secretRef:
            name: payment-secrets
        ports:
        - containerPort: 8080
```

**Base Order Service Deployment** (base/deployment-order.yaml)

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      containers:
      - name: order-service
        image: myrepo/order-service:latest
        envFrom:
        - configMapRef:
            name: order-config
        - secretRef:
            name: order-secrets
        ports:
        - containerPort: 8081
```

**Base Services** (base/service-payment.yaml & base/service-order.yaml)

```bash
apiVersion: v1
kind: Service
metadata:
  name: payment-service
spec:
  selector:
    app: payment-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8081
```

**Base Ingress Configuration** (base/ingress.yaml)

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ecommerce-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: ecommerce.local
    http:
      paths:
      - path: /payment
        pathType: Prefix
        backend:
          service:
            name: payment-service
            port:
              number: 80
      - path: /order
        pathType: Prefix
        backend:
          service:
            name: order-service
            port:
              number: 80
```

**Base** kustomization.yaml

```bash
resources:
- deployment-payment.yaml
- deployment-order.yaml
- service-payment.yaml
- service-order.yaml
- ingress.yaml
```

---

**3. Environment-Specific Configurations**

**Dev Overlay**

overlays/dev/kustomization.yaml

```bash
resources:
- ../../base

patches:
- path: patch-deployment.yaml

configMapGenerator:
- name: payment-config
  literals:
    - ENV=dev
    - PAYMENT_GATEWAY_URL=https://dev-payment.com

- name: order-config
  literals:
    - ENV=dev
    - ORDER_SERVICE_URL=https://dev-order.com

secretGenerator:
- name: payment-secrets
  literals:
    - DB_PASSWORD=dev-password

- name: order-secrets
  literals:
    - DB_PASSWORD=dev-order-password
```

overlays/dev/patch-deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payment-service
spec:
  replicas: 1
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 1
```

---

**Prod Overlay**

overlays/prod/kustomization.yaml

```bash
resources:
- ../../base

patches:
- path: patch-deployment.yaml

configMapGenerator:
- name: payment-config
  literals:
    - ENV=prod
    - PAYMENT_GATEWAY_URL=https://prod-payment.com

- name: order-config
  literals:
    - ENV=prod
    - ORDER_SERVICE_URL=https://prod-order.com

secretGenerator:
- name: payment-secrets
  literals:
    - DB_PASSWORD=prod-password

- name: order-secrets
  literals:
    - DB_PASSWORD=prod-order-password
```

overlays/prod/patch-deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payment-service
spec:
  replicas: 3
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
```

---

**4. Applying Kustomize Configurations**

**Apply Dev Environment**

```bash
kubectl apply -k overlays/dev
```

Apply Prod Environment

```bash
kubectl apply -k overlays/prod
```

---

**5. Testing the Setup**

**Check Deployments**

```bash
kubectl get deployments
```

**Check Services**

```bash
kubectl get services
```

**Check Ingress**

```bash
kubectl get ingress
```

**Access Services**

If using minikube, enable ingress:

```bash
minikube addons enable ingress
```

Then, add the following to /etc/hosts:

```bash
127.0.0.1 ecommerce.local
```

Access:

•	Payment Service: 

  http://ecommerce.local/payment
  
•	Order Service: 

  http://ecommerce.local/order

---

**Summary**

✅ Used Kustomize to manage microservices-based deployments

✅ Configured Dev and Prod environments separately

✅ Managed secrets and ConfigMaps dynamically

✅ Integrated Ingress for service routing


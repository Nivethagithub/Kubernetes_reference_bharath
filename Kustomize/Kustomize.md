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

**7. Conclusion**

Kustomize is a powerful Kubernetes-native tool for **managing multiple environments** efficiently. It helps DevOps engineers keep Kubernetes manifests clean, modular, and reusable.

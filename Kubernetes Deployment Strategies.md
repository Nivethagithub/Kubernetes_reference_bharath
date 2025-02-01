**Kubernetes Deployment Strategies**

Kubernetes provides multiple deployment strategies to enable smooth application updates, rollback capabilities, and zero-downtime deployments. Below are the key Kubernetes Deployment Strategies, along with their deep-dive explanations and how to implement each:

**1. Recreate Deployment Strategy**

**Use Case:**

Suitable for non-mission-critical applications where downtime is acceptable.

Used when stateful applications cannot handle multiple versions running simultaneously.

**How it Works:**

Kubernetes terminates all existing pods before creating new ones.

Causes downtime as there is no running pod during the deployment.

**Implementation in Kubernetes:**

 ```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: recreate-deployment
spec:
  strategy:
    type: Recreate  # This enforces the Recreate strategy
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:v2
 ```

---

**2. Rolling Update Deployment Strategy (Default)**

**Use Case:**

Used for stateless applications that need a smooth transition from the old version to the new version.

Ensures zero downtime.

**How it Works:**

Deploys new pods in batches while gradually terminating old pods.

The process is controlled using maxSurge and maxUnavailable parameters.

**Implementation in Kubernetes:**

 ```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-update-deployment
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1           # Specifies the max number of extra pods
      maxUnavailable: 1      # Specifies the max number of unavailable pods
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:v2
 ```

**How to Rollback a Rolling Update:**

 ```bash
kubectl rollout undo deployment rolling-update-deployment
 ```

---


**3. Blue-Green Deployment Strategy**

**Use Case:**

Used in critical production applications where instant rollback is required.

Zero downtime deployment with immediate rollback capability.

**How it Works:**

A new version (green) of the application is deployed parallelly with the existing version (blue).

Traffic is switched to the new version after validation.

**Implementation in Kubernetes:**

We create two Deployments (blue and green) and switch traffic using a Service.

**Blue Deployment (Current Version)**

 ```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: myapp
        image: myapp:v1
 ```

**Green Deployment (New Version)**

 ```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: green-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: myapp
        image: myapp:v2
 ```

**Service to Route Traffic**

 ```bash
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
    version: blue   # Change this to "green" when switching versions
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
 ```

**Switching from Blue to Green**

 ```bash
kubectl patch service myapp-service -p '{"spec": {"selector": {"app": "myapp", "version": "green"}}}'
 ```

---

**4. Canary Deployment Strategy**

**Use Case:**

Used for testing new versions with a subset of users before full rollout.

Reduces the risk of breaking production with faulty updates.

**How it Works:**

A small number of pods with the new version are deployed alongside the old version.

Traffic is gradually increased to the new version if it works fine.

**Implementation in Kubernetes:**

**Deploy the initial stable version:**

 ```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-canary
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: stable
  template:
    metadata:
      labels:
        app: myapp
        version: stable
    spec:
      containers:
      - name: myapp
        image: myapp:v1
 ```

**Deploy the canary version (only 1 pod initially):**

 ```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-canary-new
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
      version: canary
  template:
    metadata:
      labels:
        app: myapp
        version: canary
    spec:
      containers:
      - name: myapp
        image: myapp:v2
 ```

**Use a Service to distribute traffic:**

 ```bash
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
 ```

**Gradually increase traffic to the canary version:**

 ```bash
kubectl scale deployment myapp-canary-new --replicas=2
 ```

**Promote to full release if successful:**

 ```bash
kubectl delete deployment myapp-canary
kubectl scale deployment myapp-canary-new --replicas=3
 ```

---

**5. A/B Testing Deployment Strategy**

**Use Case:**

Used when you need to test different versions with different user segments.
Mostly used with ingress controllers or service meshes.

**How it Works:**

Traffic is split based on user-specific conditions (e.g., header-based routing).

Requires service mesh tools like Istio or Traefik.

**Implementation in Kubernetes (Istio Example):**

 ```bash
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp-virtualservice
spec:
  hosts:
  - myapp.example.com
  http:
  - match:
    - headers:
        user-type:
          exact: "beta-user"
    route:
    - destination:
        host: myapp-service
        subset: v2
  - route:
    - destination:
        host: myapp-service
        subset: v1
 ```

---

**6. Shadow Deployment Strategy**

**Use Case:**

Used for testing new versions with real user traffic without impacting them.

Ideal for performance testing in production.

**How it Works:**

New version receives a copy of live traffic, but responses are ignored.

Requires service mesh tools like Istio.

**Implementation in Istio:**

 ```bash
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp-shadow
spec:
  hosts:
  - myapp.example.com
  http:
  - route:
    - destination:
        host: myapp-service
        subset: stable
    mirror:
      host: myapp-service
      subset: new
 ```

---

**Conclusion**

**Use Recreate** for non-critical applications.

**Use Rolling Updates** for standard deployments.

**Use Blue-Green** for instant rollback.

**Use Canary** to test updates gradually.

**Use A/B Testing** for feature testing.

**Use Shadow** for testing without impacting users.

**Kubernetes Autoscaling**

**What is Autoscaling in Kubernetes?**

Kubernetes **autoscaling** is a feature that dynamically adjusts the number of pods, their resource requests, or both, depending on workload demand. Autoscaling ensures efficient resource utilization, reduces costs, and improves application performance.

There are **three types of autoscaling** in Kubernetes:

1.	**Horizontal Pod Autoscaler (HPA)** - Increases or decreases the number of pod replicas.
  
2.	**Vertical Pod Autoscaler (VPA)** - Adjusts resource requests and limits for pods dynamically.
  
3.	**Kubernetes Event-Driven Autoscaler (KEDA)** - Scales workloads based on external events.

---

**1. Horizontal Pod Autoscaler (HPA)**

**Overview**

•	HPA **scales the number of pod replicas** based on CPU, memory, or custom metrics.

•	It ensures that your application has enough pods to handle high traffic and scales down during low traffic.

**Configuration**

To enable HPA, ensure you have the **Metrics Server** installed in your cluster.

**Step 1: Install Metrics Server**

Run the following command to deploy Metrics Server:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

**Step 2: Deploy an Example Application**

Create a deployment with an Nginx web server:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        resources:
          requests:
            cpu: "100m"
          limits:
            cpu: "500m"
```

Apply the deployment:

```bash
kubectl apply -f nginx-deployment.yaml
```

**Step 3: Create an HPA Resource**

```bash
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

Apply the HPA:

```bash
kubectl apply -f hpa.yaml
```

Now, when CPU utilization exceeds **50%**, the number of replicas will **increase**.

**Real-World Example**

•	**E-commerce website (Flipkart, Amazon)**: During a flash sale, HPA automatically increases web server replicas to handle increased traffic.

•	**API services**: Scales up during peak API request times and scales down during off-hours.

---

**2. Vertical Pod Autoscaler (VPA)**

**Overview**

•	VPA **adjusts CPU and memory requests** of existing pods based on actual usage.

•	It prevents over-provisioning and ensures optimal resource allocation.

**Configuration**

**Step 1: Install VPA**

```bash
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/latest/download/vertical-pod-autoscaler.yaml
```

**Step 2: Deploy an Example Application**

Use the **same Nginx deployment** but remove static CPU requests:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

**Step 3: Create a VPA Resource**

```bash
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: nginx-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
    - containerName: nginx
      minAllowed:
        cpu: "50m"
        memory: "50Mi"
      maxAllowed:
        cpu: "500m"
        memory: "500Mi"
```

Apply the VPA:

```bash
kubectl apply -f vpa.yaml
```

Now, Kubernetes will **automatically adjust** CPU and memory requests based on real-time usage.

**Real-World Example**

**•	Database services (MongoDB, PostgreSQL)**: If a database pod needs more memory, VPA increases its memory allocation without manual intervention.

**•	Microservices**: Optimizes backend services that have unpredictable CPU/memory consumption.

---

**3. Kubernetes Event-Driven Autoscaler (KEDA)**

**Overview**

•	KEDA scales workloads based on **external events** (e.g., Kafka messages, AWS SQS, Prometheus alerts).

•	It can scale **to zero** when no events are detected.

**Configuration***

**Step 1: Install KEDA**

```bash
helm repo add kedacore https://kedacore.github.io/charts
helm repo update
helm install keda kedacore/keda --namespace keda --create-namespace
```

**Step 2: Deploy an Application Using RabbitMQ**

Deploy a simple worker app:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: worker-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: worker
  template:
    metadata:
      labels:
        app: worker
    spec:
      containers:
      - name: worker
        image: my-worker-app
        env:
        - name: QUEUE_NAME
          value: "orders"
```

**Step 3: Create a KEDA Scaler for RabbitMQ**

```bash
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: rabbitmq-scaler
spec:
  scaleTargetRef:
    kind: Deployment
    name: worker-app
  minReplicaCount: 1
  maxReplicaCount: 10
  triggers:
  - type: rabbitmq
    metadata:
      protocol: amqp
      queueName: orders
      host: amqp://my-rabbitmq.default.svc.cluster.local
```

Apply the KEDA scaler:

```bash
kubectl apply -f keda.yaml
```

Now, if new messages arrive in the RabbitMQ queue, **KEDA will scale up** the worker pods.

**Real-World Example**

•	**E-commerce order processing**: Scale worker pods based on the number of pending orders in a RabbitMQ queue.

•	**IoT data ingestion**: Scale based on the number of incoming events in an Azure Event Hub.

---

**Conclusion**

•	**Use HPA** if your application has **fluctuating traffic** and needs **more replicas** dynamically.

•	**Use VPA** if you need to **optimize CPU/memory requests** for stateful workloads like databases.

•	**Use KEDA** if you have an **event-driven system** where scaling depends on **external queues or messages.**

You can **combine them** (e.g., **HPA + VPA or HPA + KEDA**) for hybrid scaling strategies.

**Kubernetes pod.yaml File Structure**

A Pod YAML is usually the simplest unit of deployment in Kubernetes—though in practice you more commonly create Deployments or ReplicaSets (which then manage Pods). Nevertheless, understanding a Pod specification provides a solid foundation for working with Kubernetes.

---

**Basic Structure**

A typical Pod YAML includes the following top-level keys:

```bash
apiVersion: v1
kind: Pod
metadata:
  ...
spec:
  ...
```

**1. apiVersion**: Defines which version of the Kubernetes API you’re using. For Pods, it’s usually v1.

**2. kind**: Specifies the Kubernetes object type. In this case, Pod.

**3. metadata**: Contains data that helps uniquely identify the object, including:

  **name:** The name of the Pod.
     
  **namespace**: (Optional) The namespace for the Pod. If omitted, the default namespace is used.
     
  **labels**: Key-value pairs used for organization and selection.
     
  **annotations**: Key-value metadata used by tools or libraries.
  
**4. spec**: Defines the desired state of the Pod—primarily, the containers that will run inside the Pod, plus other configuration like volumes and networking.

Let’s break down an example in detail:

```bash
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
    environment: production
spec:
  containers:
    - name: my-container
      image: nginx:1.21.6
      ports:
        - containerPort: 80
          name: http
      env:
        - name: ENVIRONMENT
          value: "production"
      resources:
        limits:
          cpu: "500m"
          memory: "128Mi"
        requests:
          cpu: "250m"
          memory: "64Mi"
      volumeMounts:
        - name: my-volume
          mountPath: /usr/share/nginx/html
  volumes:
    - name: my-volume
      emptyDir: {}
  restartPolicy: Always
  nodeSelector:
    disktype: ssd
  hostNetwork: false
  dnsPolicy: ClusterFirst
```

We’ll dissect the **metadata** and **spec** sections in more detail.

---

**metadata Section**

**metadata.name**: The unique name of the Pod within its namespace.

**metadata.labels**: Labels are crucial for grouping resources. You can query or filter Pods based on these labels (e.g., using kubectl get pods -l app=my-app).

**Annotations**

**metadata.annotations** (not shown in the example above) can hold additional metadata or instructions, such as hints for logging agents, sidecar injectors, or custom controllers. They are not used for selection or grouping like labels are, but can be used by tools to store arbitrary non-identifying information.

---

**spec Section**

This is where the bulk of the configuration for your Pod resides.

**spec.containers**

A Pod can have one or more containers, but typically it has one “main” container. Each container is specified as an item in the containers list:

**name:** A unique name for the container, used inside the Pod configuration.

**image:** The container image name (and optionally a tag or digest).

**ports:** A list of ports to expose from the container. Each port can have:

**containerPort:** The port number inside the container.

**name**: A string name for the port (used in service definitions, for example).

**protoco**l: Usually TCP or UDP.

**env:** An array of environment variable definitions for the container. You can specify them directly (name, value) or from config maps or secrets.

**resources**: Defines resource requests (minimum resources needed) and limits (maximum resources allowed) for CPU and memory.

**volumeMounts**: Defines where in the container’s filesystem the volumes will be mounted. Each volumeMount references a volume specified in the top-level volumes list by name.

**Other Container Fields**

**command** and **args**: Override the default entrypoint and command arguments of the container image.

**livenessProbe, readinessProbe, startupProbe**: Health checks that Kubernetes uses to know if the container is healthy or ready to serve traffic.

**securityContext**: Configure security-related settings like user/group IDs, SELinux labels, or read-only file system.

**imagePullPolicy**: Tells Kubernetes when to pull the container image. Common values:

Always

IfNotPresent

Never

**spec.volumes**

Volumes let containers share data or maintain data beyond container restarts (depending on volume type). Each entry in volumes has:

**name**: Volume identifier referenced by the container’s volumeMounts.

: The type of volume being used. Examples:

**emptyDir**: Ephemeral storage. Emptied after the Pod is no longer running.

**hostPath**: Mounts a directory from the host node’s filesystem into the Pod.

**configMap**: Mounts a Kubernetes ConfigMap as files.

**secret**: Mounts a Kubernetes Secret as files.

**persistentVolumeClaim**: Links to a dynamically or statically provisioned Persistent Volume for stable storage.

**spec.restartPolicy**

Defines how Kubernetes should react if a container in the Pod terminates. Common values:

Always (default)

OnFailure

Never

**spec.nodeSelector / nodeAffinity**

**nodeSelector**: A simple key-value match requiring that the Pod run on a node with certain labels.

**affinity**: A more complex method for defining advanced scheduling rules.

**spec.hostNetwork**

**hostNetwork**: If set to true, the Pod uses the node’s network namespace. This is commonly used for system daemons or containers that need low-level network access. By default, it’s false.

**spec.dnsPolicy**

**dnsPolicy**: Configures DNS for the Pod. Common values:

ClusterFirst (default)

Default

None

ClusterFirstWithHostNet (when hostNetwork is true)

**spec.securityContext**

**securityContext**: Settings that apply to the entire Pod (or you can define it at the container level). Examples include Linux capabilities, SELinux, user/group IDs, etc.

---

**Putting It All Together**

Here’s an annotated example that showcases many of these fields:

```bash
apiVersion: v1
kind: Pod
metadata:
  name: sample-pod
  namespace: my-namespace
  labels:
    app: sample
    tier: backend
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "80"
spec:
  containers:
  - name: main-container
    image: myrepo/myimage:latest
    ports:
      - containerPort: 80
        name: http
    env:
      - name: ENV
        value: "production"
      - name: DB_URL
        valueFrom:
          secretKeyRef:
            name: db-secret
            key: database_url
    resources:
      requests:
        cpu: "250m"
        memory: "128Mi"
      limits:
        cpu: "500m"
        memory: "256Mi"
    livenessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 20
    volumeMounts:
      - name: config-volume
        mountPath: /etc/config
      - name: log-volume
        mountPath: /var/log/myapp
    securityContext:
      runAsUser: 1000
      runAsGroup: 3000
      readOnlyRootFilesystem: true
  
  volumes:
    - name: config-volume
      configMap:
        name: my-config-map
    - name: log-volume
      emptyDir: {}
  
  restartPolicy: Always
  
  nodeSelector:
    disktype: ssd
  
  dnsPolicy: ClusterFirst
  
  securityContext:
    fsGroup: 2000
```

**1. metadata:**

name: sample-pod uniquely identifies the Pod in my-namespace.

labels: app=sample, tier=backend for organizational and selection purposes.

annotations: For external integrations (Prometheus scraping in this example).

**2. spec**:

containers: A list of containers. We have one named main-container.

image: myrepo/myimage:latest.

ports: Exposing port 80.

env: Setting environment variables; note the valueFrom.secretKeyRef to pull values from a Secret.

resources: CPU and memory requests and limits.

livenessProbe: Checks /health via HTTP every 20 seconds, with an initial 15-second delay.

volumeMounts: Mount a config map and an emptyDir for logs.

securityContext: Container-level security settings.

volumes: Two volumes declared:

config-volume: Sourced from a ConfigMap.

log-volume: An emptyDir used for ephemeral data.

restartPolicy: Always restarts the container on failure.

nodeSelector: Schedules the Pod to nodes labeled with disktype=ssd.

dnsPolicy: ClusterFirst, the default policy for Pods.

securityContext: Pod-level security settings, including fsGroup=2000.

**Summary**

A **Pod** is the smallest deployable unit in Kubernetes, often wrapping a single container.

The **.spec** field defines how that container is run—what image to use, how resources are allocated, any volumes to mount, etc.

The **.metadata** section organizes and identifies your Pod with name, labels, and annotations.

**Volumes** and **volumeMounts** provide persistent or ephemeral storage.

**Probes** (liveness, readiness, startup) allow Kubernetes to manage the Pod’s lifecycle and health.

**SecurityContext** and other spec fields let you specify advanced features around security, networking, and scheduling.

Understanding Pod YAML structure is fundamental because higher-level objects like Deployments, ReplicaSets, StatefulSets, or DaemonSets incorporate the Pod template in their specifications. Once you grasp the details of writing and interpreting a Pod spec, you’ll have a much easier time configuring other Kubernetes resources.

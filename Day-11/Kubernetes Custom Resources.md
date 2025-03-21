**Custom Resources in Kubernetes**

**Introduction**

Kubernetes offers a rich set of native APIs like Deployments, Services, Pods, ConfigMaps, and Secrets. However, as cloud-native adoption grows, so do the demands on Kubernetes—demands that go beyond its default capabilities. To meet these advanced use cases, Kubernetes provides a way to extend its API through **Custom Resource Definitions (CRDs) and Custom Controllers**.

In this article, we will explore:

•	What are CRDs, Custom Resources, and Custom Controllers?

•	Why and when to extend Kubernetes APIs

•	Real-world examples like Istio and ArgoCD

•	Step-by-step workflow for implementing CRDs

•	Best practices and tools for managing them

•	How DevOps Engineers can contribute to and manage these components effectively

This guide is ideal for DevOps Engineers, Cloud Engineers, and aspiring Kubernetes developers looking to deepen their understanding of Kubernetes extensibility.

---

**What is a Custom Resource Definition (CRD)?**

A **Custom Resource Definition (CRD)** allows you to define a new type of Kubernetes resource. It enables developers and platform teams to extend the Kubernetes API without modifying the Kubernetes core.

**Example Use Case**

Let’s say your organization wants to integrate Istio, a service mesh, into your Kubernetes cluster. Istio introduces a resource called VirtualService, which is not part of Kubernetes by default. To support this:

•	A CRD is created that defines the structure of the VirtualService

•	Kubernetes API now understands what a VirtualService is

```sh
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: my-service
spec:
  hosts:
  - my-service.example.com
  http:
  - route:
    - destination:
        host: my-service
```

---

**What is a Custom Resource (CR)?**

Once a CRD is registered, users can create **Custom Resources** based on it. A Custom Resource (CR) is the actual instance of the newly defined type.

Using the previous example:

•	The CRD defines VirtualService

•	A user creates a VirtualService custom resource that routes traffic

Think of a CRD as a class definition, and CRs as instances of that class.

---

**What is a Custom Controller?**

A **Custom Controller** monitors the state of Custom Resources and performs operations to ensure that the current state matches the desired state.

**Why It’s Needed**

Even if a CR is created and validated, no action happens until a controller processes it. For example:

•	You define a VirtualService

•	Without the Istio controller, the configuration has no effect

•	The controller watches for changes and reconciles the state

---

**Native vs Custom Resources in Kubernetes**

| Native Kubernetes        | Custom Kubernetes                     |
|--------------------------|----------------------------------------|
| Deployment, Service, Pod | Istio VirtualService, ArgoCD Application |
| Handled by built-in controllers | Requires custom controllers        |
| Validated by Kubernetes' internal schema | Validated against CRD         |

---

**Workflow for Deploying CRDs and Custom Resources**

**1. DevOps Engineer Deploys the CRD**

•	Use Helm, plain YAML, or Operator SDK

•	CRD is registered with Kubernetes

```sh
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
helm install istio-base istio/base -n istio-system --create-namespace
```

**2. User or Engineer Creates a Custom Resource**

```sh
kubectl apply -f virtual-service.yaml
```

•	CR is validated against the CRD

•	Stored in Kubernetes if valid

**3. DevOps Engineer Deploys the Custom Controller**

•	Watches CRs and takes action

•	Deployed using Helm or Operator SDK

```sh
helm install istiod istio/istiod -n istio-system
```

---

**Real-World Examples of CRDs and Controllers**

**Istio**

•	CRD: Defines resources like VirtualService, DestinationRule

•	Controller: Istio controller reconciles and applies mesh configuration

**Argo CD**

•	CRD: Application

•	Controller: Syncs app manifests from Git repositories

**Others**

•	Prometheus Operator

•	FluxCD

•	Kyverno (for policy enforcement)

---

**Writing Your Own Custom Controller (Advanced)**

Most DevOps Engineers don't write controllers, but understanding the basics helps in debugging and contributing to open-source projects.

**Technologies Used**

•	**Language**: Golang (preferred)

•	**Kubernetes Client**: client-go

•	**Controller Framework**: https://github.com/kubernetes-sigs/controller-runtime

**Key Concepts**

•	**Watchers**: Observe changes to resources

•	**Work Queue**: Processes events in sequence

•	**Reflectors**: Maintain a local cache of resources

For a working example:

•	Kubernetes official repo: (https://github.com/kubernetes/sample-controller)

---

**Best Practices for Managing CRDs**

•	Always version your CRDs using apiextensions.k8s.io/v1

•	Use Helm or Operator SDK for repeatable deployments

•	Validate CRDs with schema to avoid malformed CRs

•	Limit CR access using RBAC

•	Monitor CRDs using Prometheus metrics

---

**Conclusion**

Custom Resources, CRDs, and Custom Controllers are essential tools for extending the Kubernetes ecosystem. Whether you are integrating third-party tools like Istio, ArgoCD, or Prometheus—or building your own operator—understanding how these components interact is vital for any DevOps or Cloud Engineer.

As a DevOps Engineer:

•	You may not write custom controllers, but you will often deploy and debug them
•	Learn to read CRD schemas and validate YAML manifests
•	Use Helm and GitOps tools to manage these resources declaratively
By mastering this part of Kubernetes, you'll unlock powerful new capabilities and contribute more effectively to scalable, modern infrastructure.

---

**GitHub Repository Recommendation**

If you're maintaining a GitHub repo, consider structuring your directories like this:

```sh
kubernetes/
├── crd-examples/
│   ├── istio-virtualservice.yaml
│   ├── prometheus-crds.yaml
├── custom-controllers/
│   ├── README.md
│   └── links-to-official-docs.md
```

This structure helps others explore and contribute to your repository efficiently.

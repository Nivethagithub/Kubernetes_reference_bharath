**Kubernetes Monitoring with Prometheus and Grafana**

Monitoring is a fundamental aspect of modern DevOps practices. In this guide, we explore Kubernetes monitoring using Prometheus and Grafana. You’ll learn not only the “how,” but also the “why” behind monitoring, backed by practical examples, Helm installations, and real-world use cases. This article is ideal for DevOps engineers, SREs, and cloud practitioners looking to implement effective monitoring for Kubernetes clusters.

---

**Why Kubernetes Monitoring Matters**

In modern environments, a single Kubernetes cluster may be shared across multiple teams and environments (Dev, Staging, Prod). Issues like services not receiving traffic or deployments failing can go unnoticed without proper monitoring.

**Key reasons for monitoring:**

•	Ensure high availability and performance

•	Detect issues proactively

•	Provide visibility into application health

•	Support alerting and incident response

•	Enable capacity planning and scaling decisions

---

**Prometheus: Metrics Collection for Kubernetes**

Prometheus is an open-source monitoring and alerting toolkit initially developed by SoundCloud and now a CNCF project.

**Prometheus Architecture**

•	**Prometheus Server**: Pulls metrics via HTTP from configured endpoints (e.g., /metrics).

•	**Time-Series DB**: Stores metrics in a time-series database format.

•	**Alertmanager**: Handles alerts and can integrate with Slack, Email, etc.

•	**PromQL**: Prometheus Query Language for querying metrics.

```sh
# Sample Prometheus scrape config snippet
scrape_configs:
  - job_name: 'kubernetes'
    static_configs:
      - targets: ['<NODE_IP>:<PORT>']
```

---

**Grafana: Visualization Layer**

Grafana provides beautiful dashboards and visualizations using data sources like Prometheus.

**Why Grafana:**

•	Easy integration with Prometheus

•	Supports custom and community dashboards

•	Alerting capabilities

•	User-friendly UI for non-technical stakeholders

---

**Setting Up a Kubernetes Cluster with Minikube**

Minikube is used for local development and testing.

```sh
minikube start --memory=4096 --driver=hyperkit
```

Check cluster status:

```sh
kubectl get pods -A
```

---

**Installing Prometheus Using Helm**

Add the Prometheus Helm repo:

```sh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Install Prometheus:

```sh
helm install prometheus prometheus-community/prometheus
```

Expose Prometheus service to nodeport for accessing via browser:

```sh
kubectl expose service prometheus-server \
  --type=NodePort \
  --target-port=9090 \
  --name=prometheus-server-ext
```

Access Prometheus UI:

```sh
minikube ip  # get IP
# Visit: http://<minikube-ip>:<node-port>
```

---

**Installing Grafana and Connecting to Prometheus**

Add Grafana Helm repo:

```sh
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

Install Grafana:

```sh
helm install grafana grafana/grafana
```

Expose Grafana to nodeport for accessing via browser: (In production this step isn't required, sice we use ingress. Create rules and start using grafana)

```sh
kubectl expose service grafana \
  --type=NodePort \
  --target-port=3000 \
  --name=grafana-ext
```

Get Grafana password from the terminal command:

```sh
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```

Login via http://<minikube-ip>:<node-port> and configure Prometheus as a data source.

---

**Understanding and Using kube-state-metrics**

Kube-state-metrics exposes detailed Kubernetes object states (deployments, pods, services).

**Why use it?**

•	Collect deployment replica status

•	Track pod restarts

•	Monitor service availability beyond API server metrics

**Expose service:**

```sh
kubectl expose service prometheus-kube-state-metrics \
  --type=NodePort \
  --target-port=8080 \
  --name=kube-state-metrics-ext
```

Access metrics via:

```sh
http://<minikube-ip>:<node-port>/metrics
```

---

**Sync/connect grafana with prometheus:**

1. Go to data source option.
2. Select prometheus
3. Add the IP address of the  prometheus with port
4. click save and test.

---
**Creating Dashboards in Grafana**

Instead of building from scratch, import a community dashboard:

**1.**	Click “Import” in Grafana

**2.**	Use Dashboard ID: 3662 ## This ID is a pre-existing template to retrieve the data from prometheus. It's a template of pre-defined promQL query.

**3.**	Select Prometheus as the data source. 
This pre-built dashboard displays:

•	Node CPU/memory usage

•	API server availability

•	etcd performance

•	Pod/container stats

---

**Adding Custom Application Metrics**

To monitor custom applications:

**1.**	Developers must expose a /metrics endpoint using Prometheus client libraries.

**2.**	Update Prometheus ConfigMap to add a new scrape target:

```sh
- job_name: 'custom-app'
  static_configs:
    - targets: ['<app-service>:<port>']
```

1.	Redeploy Prometheus or update the ConfigMap dynamically.

Prometheus libraries are available for:

•	Go

•	Python

•	Java

•	Node.js

Prometheus Client Libraries Documentation (https://prometheus.io/docs/instrumenting/clientlibs/)

---

**Best Practices**

•	Use **Helm** or **Operators** for lifecycle management of Prometheus/Grafana

•	Set up **Alertmanager** for incident response

•	Secure endpoints using Ingress + TLS

•	Use **kube-state-metrics** for enhanced insights

•	Maintain dashboard as code using **Grafana-as-Code**

---

**Conclusion**

Monitoring Kubernetes with Prometheus and Grafana provides comprehensive visibility into your infrastructure and applications. This setup is scalable, customizable, and open-source — making it ideal for DevOps and cloud-native teams.

By leveraging tools like kube-state-metrics and PromQL queries, you can extract granular metrics and visualize them using Grafana. This solution can evolve with your environments, from local Minikube clusters to production-grade multi-cluster systems.

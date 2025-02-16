**Helm Chart Structure**

Helm is a package manager for Kubernetes that simplifies deployment and management of applications. A Helm chart is a collection of files that define a related set of Kubernetes resources.

---

**Helm Chart Directory Structure**

```bash
<helm_chart_name>/ _________________________________  Helm chart root directory
├── chart.yaml ______________________________________ Chart metadata (name, version, description)
├── LICENSE ________________________________________ License information for the chart
├── README.md ______________________________________ Documentation and usage instructions
├── values.yaml ____________________________________ Default config values for customizing templates
├── values.schema.json ____________________________ JSON schema for validating values.yaml input
│
├── charts/ ________________________________________ Holds chart dependencies
│
├── templates/ _____________________________________ Kubernetes resource templates using Go
│   ├── deployment.yaml ____________________________ Deployment resource template
│   ├── service.yaml _______________________________ Service resource template
│   ├── ingress.yaml _______________________________ Ingress resource template
│   ├── _helpers.tpl _______________________________ Helper template for reusable content
│   ├── NOTES.txt __________________________________ Instructions displayed after chart installation
```

---

**Helm Chart Directory Structure**

**1. example_chart/ (Helm Chart Root Directory)**

•	This is the root directory of the Helm chart.

•	It contains all the necessary files and subdirectories required to define, configure, and deploy the application.

---

**Chart Metadata and Configuration Files**

**2. chart.yaml (Chart Metadata)**

•	This file contains essential metadata about the Helm chart, such as:

o	Name of the chart

o	Version

o	Description

o	API version

o	App version

o	Maintainers, dependencies, etc.

•	Example:

```bash
apiVersion: v2
name: techopsexampleschart
description: A Helm chart for deploying a sample application
type: application
version: 1.0.0
appVersion: 1.0.0
```

**3. LICENSE (License Information)**

•	Defines the license under which the chart is distributed.

•	Useful for sharing charts publicly or within an organization.

**4. README.md (Documentation and Usage)**

•	A markdown file that provides documentation about the chart.

•	Contains:

o	Instructions on how to install/uninstall the chart

o	Configuration options

o	Examples of how to use it

**5. values.yaml (Default Configuration Values)**

•	This file contains the default values for chart customization.

•	It is used to override configurations dynamically during deployment.

•	Example:

```bash
replicaCount: 2
image:
  repository: nginx
  tag: latest
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80
```

**6. values.schema.json (Validation for values.yaml)**

•	A JSON schema that validates the values.yaml file.

•	Ensures that users provide correct input values.

```bash
  Example:

```bash
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "replicaCount": {
      "type": "integer",
      "minimum": 1
    }
  }
}
```

---

**Chart Dependencies**

**7. charts/ (Dependencies Directory)**

•	This folder holds dependencies for the Helm chart.

•	If the chart relies on other charts (e.g., databases like MySQL or Redis), those charts are stored here.

•	Dependencies are managed in Chart.yaml and downloaded using helm dependency update.

---

**Templates (Kubernetes Resource Definitions)**

**8. templates/ (Kubernetes Resource Templates)**

•	This directory contains all the Kubernetes manifests (YAML files) used to deploy the application.

•	These templates use Go templating syntax ({{ }}) to make them dynamic.

**9. deployment.yaml (Deployment Template)**

•	Defines the Kubernetes Deployment resource.

•	Uses values from values.yaml for configuration.

•	Example:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Chart.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
```

**10. service.yaml (Service Template)**

•	Defines the Kubernetes Service resource.
•	Exposes the application internally or externally.

•	Example:

```bash
apiVersion: v1
kind: Service
metadata:
  name: {{ .Chart.Name }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.port }}
  selector:
    app: {{ .Chart.Name }}
```
**11. _helpers.tpl (Reusable Templates)**

•	Contains reusable template functions for labels, names, and common configurations.

•	Example:

```bash
{{- define "fullname" -}}
{{ .Chart.Name }}-{{ .Release.Name }}
{{- end -}}
```

**12. NOTES.txt (Post Installation Instructions)**

•	This file contains instructions displayed after chart installation.

•	Helps users understand how to interact with the deployed application.

•	Example:

```bash
The application has been successfully deployed.
To access it, run:

kubectl port-forward svc/{{ .Chart.Name }} 8080:80
```

---

**Summary**

**1.	Chart Metadata** (chart.yaml) – Defines name, version, and dependencies.

**2.	Configuration** (values.yaml) – Default values for customization.

**3.	Validation** (values.schema.json) – Ensures input correctness.

**4.	Documentation** (README.md, LICENSE) – Provides guidance and legal info.

**5.	Templates** (templates/) – Defines Kubernetes resources dynamically using Helm's templating engine.

**6.	Dependencies** (charts/) – Stores required Helm charts.

**7.	Helper Templates** (_helpers.tpl) – Reusable templates to avoid redundancy.

**8.	Post Installation Notes** (NOTES.txt) – Instructions displayed after deployment.

This Helm chart structure allows DevOps engineers to manage Kubernetes applications efficiently, ensuring scalability, consistency, and maintainability.

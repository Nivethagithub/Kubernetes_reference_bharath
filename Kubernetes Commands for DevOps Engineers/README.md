Essential Kubernetes Commands that DevOps Engineers commonly use in their day-to-day activities:

Basic Commands
Get Cluster Information

kubectl cluster-info
Displays the address of the Kubernetes master and services running in the cluster.

List Nodes

kubectl get nodes
Lists all nodes in the cluster.

Namespace Management
List Namespaces

kubectl get namespaces
Displays all namespaces in the cluster.

Create Namespace

kubectl create namespace <namespace_name>
Creates a new namespace.

Delete Namespace

kubectl delete namespace <namespace_name>
Deletes a specified namespace.

Pod Management
List Pods

kubectl get pods
Lists all pods in the default namespace.

Describe Pod

kubectl describe pod <pod_name>
Displays detailed information about a specific pod.

Create Pod

kubectl run <pod_name> --image=<image_name>
Creates a new pod running the specified container image.

Delete Pod

kubectl delete pod <pod_name>
Deletes a specified pod.

Deployment Management
List Deployments

kubectl get deployments
Lists all deployments in the default namespace.

Create Deployment

kubectl create deployment <deployment_name> --image=<image_name>
Creates a new deployment with the specified container image.

Delete Deployment

kubectl delete deployment <deployment_name>
Deletes a specified deployment.

Service Management
List Services

kubectl get services
Lists all services in the default namespace.

Create Service

kubectl expose deployment <deployment_name> --type=LoadBalancer --name=<service_name>
Creates a new service to expose a deployment.

Delete Service

kubectl delete service <service_name>
Deletes a specified service.

ConfigMap and Secret Management
Create ConfigMap

kubectl create configmap <configmap_name> --from-literal=<key>=<value>
Creates a new ConfigMap from literal values.

Create Secret

kubectl create secret generic <secret_name> --from-literal=<key>=<value>
Creates a new Secret from literal values.

Logs and Debugging
View Pod Logs

kubectl logs <pod_name>
Displays the logs of a specific pod.

Execute Command in Pod

kubectl exec -it <pod_name> -- <command>
Executes a command in a running pod.

Scaling and Rollouts
Scale Deployment

kubectl scale deployment <deployment_name> --replicas=<number_of_replicas>
Scales a deployment to the specified number of replicas.

Rollout Status

kubectl rollout status deployment/<deployment_name>
Displays the rollout status of a deployment.

Rollout Undo

kubectl rollout undo deployment/<deployment_name>
Rolls back a deployment to a previous revision.

These commands cover a wide range of tasks from managing namespaces, pods, and deployments to handling services, ConfigMaps, and Secrets.

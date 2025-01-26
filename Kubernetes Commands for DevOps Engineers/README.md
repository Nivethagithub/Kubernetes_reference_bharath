**Essential Kubernetes Commands that DevOps Engineers commonly use in their Day-to-Day activities:**

### Basic Commands

1. **Get Cluster Information**
   
   ```sh
   kubectl cluster-info
   ```
   Displays the address of the Kubernetes master and services running in the cluster.

2. **List Nodes**
   
   ```sh
   kubectl get nodes
   ```
   Lists all nodes in the cluster.

### Namespace Management

3. **List Namespaces**
   
   ```sh
   kubectl get namespaces
   ```
   Displays all namespaces in the cluster.

4. **Create Namespace**
   
   ```sh
   kubectl create namespace <namespace_name>
   ```
   Creates a new namespace.

5. **Delete Namespace**
   
   ```sh
   kubectl delete namespace <namespace_name>
   ```
   Deletes a specified namespace.

### Pod Management

6. **List Pods**
   
   ```sh
   kubectl get pods
   ```
   Lists all pods in the default namespace.

7. **Describe Pod**
   
   ```sh
   kubectl describe pod <pod_name>
   ```
   Displays detailed information about a specific pod.

8. **Create Pod**

   ```sh
   kubectl run <pod_name> --image=<image_name>
   ```
   Creates a new pod running the specified container image.

9. **Delete Pod**
    
   ```sh
   kubectl delete pod <pod_name>
   ```
   Deletes a specified pod.

### Deployment Management

10. **List Deployments**
    
    ```sh
    kubectl get deployments
    ```
    Lists all deployments in the default namespace.

11. **Create Deployment**
    
    ```sh
    kubectl create deployment <deployment_name> --image=<image_name>
    ```
    Creates a new deployment with the specified container image.

12. **Delete Deployment**
    
    ```sh
    kubectl delete deployment <deployment_name>
    ```
    Deletes a specified deployment.

### Service Management

13. **List Services**
    
    ```sh
    kubectl get services
    ```
    Lists all services in the default namespace.

14. **Create Service**
    
    ```sh
    kubectl expose deployment <deployment_name> --type=LoadBalancer --name=<service_name>
    ```
    Creates a new service to expose a deployment.

15. **Delete Service**
    
    ```sh
    kubectl delete service <service_name>
    ```
    Deletes a specified service.

### ConfigMap and Secret Management

16. **Create ConfigMap**
    
    ```sh
    kubectl create configmap <configmap_name> --from-literal=<key>=<value>
    ```
    Creates a new ConfigMap from literal values.

17. **Create Secret**
    
    ```sh
    kubectl create secret generic <secret_name> --from-literal=<key>=<value>
    ```
    Creates a new Secret from literal values.

### Logs and Debugging

18. **View Pod Logs**
    
    ```sh
    kubectl logs <pod_name>
    ```
    Displays the logs of a specific pod.

19. **Execute Command in Pod**
    
    ```sh
    kubectl exec -it <pod_name> -- <command>
    ```
    Executes a command in a running pod.

### Scaling and Rollouts

20. **Scale Deployment**
    
    ```sh
    kubectl scale deployment <deployment_name> --replicas=<number_of_replicas>
    ```
    Scales a deployment to the specified number of replicas.

21. **Rollout Status**
    
    ```sh
    kubectl rollout status deployment/<deployment_name>
    ```
    Displays the rollout status of a deployment.

22. **Rollout Undo**
    ```sh
    kubectl rollout undo deployment/<deployment_name>
    ```
    Rolls back a deployment to a previous revision.

These commands cover a wide range of tasks from managing namespaces, pods, and deployments to handling services, ConfigMaps, and Secrets.

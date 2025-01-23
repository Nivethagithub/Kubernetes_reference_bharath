**Here are 50 Kubernetes Interview Questions from basic to advanced along with their answers for DevOps Interviews**

1. What is Kubernetes?
   
   Kubernetes is an open-source container orchestration platform designed to automate the deployment, scaling, and 
   management of containerized applications.

2. What are the key components of Kubernetes architecture?

   Master components: API Server, etcd, Scheduler, Controller Manager.
   Node components: Kubelet, Kube-proxy, Container Runtime.
   
3. What is a Pod in Kubernetes?
   
   A Pod is the smallest deployable unit in Kubernetes, representing a single instance of a running process in a container.

4. What is a Deployment in Kubernetes?
   
   A Deployment manages the lifecycle of Pods, ensuring the desired number of replicas, updates, and rollbacks.

5. What is a Service in Kubernetes?
   
   A Service provides stable networking for Pods and abstracts access to a group of Pods through a single IP or DNS name.

6. What are Namespaces in Kubernetes?
    
   Namespaces are virtual clusters within a Kubernetes cluster, used to divide resources among multiple users or teams.

7. What is etcd in Kubernetes?
    
   etcd is a distributed key-value store used by Kubernetes to store cluster configuration data and state.

8. What is a ReplicaSet?
    
   A ReplicaSet ensures a specified number of Pod replicas are running at any time.

9. How do you scale a Deployment in Kubernetes?

   Use kubectl scale deployment <deployment-name> --replicas=<number>.

10. How to check running Pods in Kubernetes?

    Use kubectl get pods.

11. What is a ConfigMap in Kubernetes?

    A ConfigMap is used to inject configuration data into Pods as environment variables, command-line arguments, or 
    configuration files.

12. What is a Secret in Kubernetes?
    
    A Secret stores sensitive data like passwords, tokens, or keys, in an encrypted format.

13. What is the difference between a StatefulSet and a Deployment?

    StatefulSet: Manages stateful applications, providing unique identities and stable network identities.
    Deployment: Manages stateless applications with identical Pods.
    
14. How does Kubernetes handle networking?
    
    Kubernetes uses a flat network model where every Pod gets a unique IP, and communication between Pods is facilitated 
    without NAT.

15. What is the role of the Kubelet?
    
    The Kubelet runs on each node and ensures the desired state of Pods as defined in the configuration.

16. What is the function of the Kubernetes Scheduler?
    
    The Scheduler assigns Pods to nodes based on resource availability and constraints.

17. How do you perform a rolling update in Kubernetes?
    
    Update the Deployment using kubectl apply -f <deployment.yaml> or modify the image tag in the YAML file.

18. How do you rollback a Deployment in Kubernetes?
    
    Use kubectl rollout undo deployment <deployment-name>.

19. What is a DaemonSet in Kubernetes?

    A DaemonSet ensures that a copy of a Pod runs on all (or selected) nodes.

20. What is Horizontal Pod Autoscaler (HPA)?
    
    HPA automatically adjusts the number of Pod replicas based on CPU/memory utilization or custom metrics.

21. What is a Persistent Volume (PV)?

    A PV is a storage resource in the cluster that provides storage abstraction for Pods.

22. What is a Persistent Volume Claim (PVC)?
    
    A PVC is a request for storage resources by a Pod.

23. How does Kubernetes handle high availability?
    
    Kubernetes ensures high availability by using multiple master nodes, replica Pods, and distributed architecture.

24. What is the difference between Ingress and LoadBalancer services?

    Ingress: Manages HTTP and HTTPS traffic to cluster services.
    LoadBalancer: Exposes services externally via a cloud provider's load balancer.
    
25. What is a Network Policy in Kubernetes?

   A Network Policy defines rules for network traffic to/from Pods.

26. How do you monitor Kubernetes clusters?

    Use tools like Prometheus, Grafana, Kubernetes Dashboard, or third-party monitoring solutions.

27. What is Helm in Kubernetes?
    
    Helm is a package manager for Kubernetes, used to deploy and manage applications as charts.

28. How do you debug failing Pods?

    Use kubectl logs <pod-name> or kubectl describe pod <pod-name>.

29. What are the types of Kubernetes probes?

    Liveness Probe: Checks if the container is alive.
    Readiness Probe: Checks if the container is ready to serve requests.
    Startup Probe: Checks if the container has started.
    
30. What is the role of RBAC in Kubernetes?

    Role-Based Access Control (RBAC) manages permissions and access to cluster resources.

31. What is the difference between a ClusterIP, NodePort, and LoadBalancer service?

    ClusterIP: Internal access within the cluster.
    NodePort: External access through a node’s port.
    LoadBalancer: External access via a cloud provider’s load balancer.
    
32. How does Kubernetes implement service discovery?
    
    Using DNS (CoreDNS) to resolve Service names to their cluster IPs.

33. What is the purpose of Taints and Tolerations?

    Taints prevent Pods from being scheduled on certain nodes, while Tolerations allow exceptions.

34. How can you drain a node in Kubernetes?

    Use kubectl drain <node-name>.

35. How does Kubernetes handle Secrets encryption?
    
    Secrets are encrypted at rest using encryption providers like KMS.

36. What is Pod Disruption Budget (PDB)?
    
    PDB ensures a minimum number of Pods remain available during voluntary disruptions.

37. How do you troubleshoot CrashLoopBackOff errors?

    Use kubectl logs and kubectl describe to analyze the issue.

38. What are Custom Resource Definitions (CRDs)?
    
    CRDs allow you to define and use custom Kubernetes resources.

39. What is Kubernetes Federation?
    
    Kubernetes Federation manages multiple clusters as a single entity.

40. How do you secure a Kubernetes cluster?
    
    Use RBAC, Network Policies, Secrets encryption, and regularly update components.

41. What is KubeProxy's role in Kubernetes networking?
    
    KubeProxy manages networking rules for services.

42. How do you perform blue-green deployments in Kubernetes?
    
    Deploy a new version alongside the old one, switch traffic to the new version, and delete the old one.

43. What is Kubernetes Operator?
    
    An Operator is a custom controller for automating operational tasks for applications.

44. How does Kubernetes handle node failures?

    Kubernetes reschedules Pods from failed nodes to healthy nodes.

45. What are Init Containers in Kubernetes?

    Init Containers run before the main containers to perform initialization tasks.

46. What is Kubernetes Audit Logging?
    
    Audit Logging tracks API server requests for debugging and security.

47. What is a Service Mesh in Kubernetes?
    
    A Service Mesh provides features like traffic management, security, and observability for microservices.

48. How do you run stateful applications in Kubernetes?
    
    Use StatefulSets with Persistent Volumes.

49. What are Admission Controllers in Kubernetes?

    Admission Controllers are plugins that intercept API requests to validate or modify them.

50. How do you perform disaster recovery in Kubernetes?

    Backup etcd data, use disaster recovery tools, and restore the cluster configuration.

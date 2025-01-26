Here’s a list of essential Kubernetes commands used by DevOps Engineers in their day-to-day activities, covering common real-world tasks like deployments, troubleshooting, resource management, and monitoring:

General Commands

Cluster Information

kubectl cluster-info
kubectl get nodes
kubectl describe node <node-name>

Namespace Management

kubectl get namespaces
kubectl create namespace <namespace-name>
kubectl delete namespace <namespace-name>
Workload Management
Viewing Deployments, Pods, and Services

kubectl get deployments
kubectl get pods
kubectl get services
kubectl get pods -n <namespace>
kubectl describe pod <pod-name>
Creating and Managing Deployments

kubectl apply -f <deployment-file>.yaml
kubectl create deployment <deployment-name> --image=<image-name>
kubectl rollout status deployment <deployment-name>
kubectl rollout undo deployment <deployment-name>
kubectl delete deployment <deployment-name>
Scaling Deployments

kubectl scale deployment <deployment-name> --replicas=<number-of-replicas>
Exposing Deployments

kubectl expose deployment <deployment-name> --type=<service-type> --port=<port> --target-port=<target-port>
Pod and Container Management
Accessing and Debugging Pods

kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec -n <namespace> <pod-name> -- <command>
Deleting Pods

kubectl delete pod <pod-name>
kubectl delete pod <pod-name> --force --grace-period=0
Checking Pod Events

kubectl get events
kubectl describe pod <pod-name>
ConfigMap and Secret Management
Creating ConfigMaps

kubectl create configmap <config-name> --from-literal=key=value
kubectl create configmap <config-name> --from-file=<file-path>
kubectl apply -f <configmap-file>.yaml
Creating Secrets

kubectl create secret generic <secret-name> --from-literal=username=<user> --from-literal=password=<pass>
kubectl apply -f <secret-file>.yaml
Viewing ConfigMaps and Secrets

kubectl get configmap
kubectl get secret
kubectl describe configmap <configmap-name>
kubectl describe secret <secret-name>
Troubleshooting
Node and Resource Issues

kubectl get nodes
kubectl describe node <node-name>
kubectl top node
kubectl top pod
Resource Utilization

kubectl top pod -n <namespace>
kubectl get hpa
Troubleshooting Errors

kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/sh
Networking
Ingress and Service Management

kubectl get ingress
kubectl get svc
kubectl describe svc <service-name>
Port Forwarding

kubectl port-forward <pod-name> <local-port>:<container-port>
DNS Troubleshooting

kubectl exec -it <pod-name> -- nslookup <service-name>
Helm Commands (Optional in DevOps Workflows)
Helm Release Management
helm install <release-name> <chart-name>
helm upgrade <release-name> <chart-name>
helm rollback <release-name>
helm delete <release-name>
Viewing Helm Releases
helm list
helm status <release-name>
Kubernetes Resource Management
Viewing and Editing Resources

kubectl get all
kubectl edit deployment <deployment-name>
kubectl describe service <service-name>
Dry Run to Preview Changes

kubectl apply -f <file>.yaml --dry-run=client
Deleting Resources

kubectl delete -f <file>.yaml
kubectl delete pod,svc <name>
Monitoring and Logging
Enabling Resource Metrics

kubectl top pod
kubectl top node
Viewing Logs

kubectl logs -f <pod-name>
kubectl logs -f <pod-name> -c <container-name>
Backup and Restore
Backing up Resources
kubectl get all -o yaml > backup.yaml
Restoring Resources
kubectl apply -f backup.yaml
These commands are essential for managing Kubernetes clusters efficiently and are widely used by DevOps Engineers for various tasks. Let me know if you’d like examples or more explanations for any specific use case.

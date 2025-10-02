# Kubernetes ReplicationController Management

This README provides a guide to managing Kubernetes ReplicationControllers using `kubectl` commands. It covers creating, inspecting, and deleting a ReplicationController, as well as listing associated pods, using both imperative commands and a YAML configuration.

## Prerequisites
- A running Kubernetes cluster
- `kubectl` installed and configured to interact with your cluster

## Understanding ReplicationController
To view the schema and details of a ReplicationController resource:

```bash
kubectl explain rc
```

## Creating a ReplicationController
### Using YAML Configuration
Below is a sample YAML configuration for a ReplicationController named `nginx-rc` that ensures 3 replicas of an `nginx` pod are running:

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

Save the above configuration as `rc.yaml` and apply it to create the ReplicationController:

```bash
kubectl apply -f rc.yaml
```

## Inspecting a ReplicationController
View detailed information about the `nginx-rc` ReplicationController:

```bash
kubectl describe rc/nginx-rc
```

## Listing Pods Managed by ReplicationController
List all pods managed by the `nginx-rc` ReplicationController using the selector:

```bash
kubectl get pods --selector=app=nginx
```

## Deleting a ReplicationController
Delete the `nginx-rc` ReplicationController (this will also terminate the managed pods):

```bash
kubectl delete rc nginx-rc
```

## Notes
- The `kubectl apply` command is idempotent and preferred for declarative management of resources.
- The `selector` in the ReplicationController must match the pod template's `labels` to manage the pods correctly.
- Use ReplicationControllers for simple replication tasks; for more advanced features, consider using Deployments or ReplicaSets in modern Kubernetes setups.
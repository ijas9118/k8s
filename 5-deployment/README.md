# Kubernetes Deployment Management

This README provides a comprehensive guide to managing Kubernetes Deployments using `kubectl` commands. It covers creating, inspecting, updating, scaling, and rolling back Deployments, as well as generating YAML configurations. Deployments are part of the `apps/v1` API group and are used to manage ReplicaSets and pods, providing declarative updates and rollback capabilities.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` installed and configured to interact with your cluster

## Understanding Deployment

To view the schema and details of a Deployment resource:

```bash
kubectl explain deploy
```

**API Group Details**:

- **Group**: apps
- **Kind**: Deployment
- **Version**: v1

## Creating a Deployment

### Using YAML Configuration

Below is a sample YAML configuration for a Deployment named `nginx-deployment` that ensures 3 replicas of an `nginx` pod are running:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
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
          image: nginx:latest
          ports:
            - containerPort: 80
```

Save the above configuration as `deploy.yaml` and apply it to create the Deployment:

```bash
kubectl apply -f deploy.yaml
```

### Generating YAML Configuration

Generate a YAML configuration for a Deployment without creating it:

```bash
kubectl create deploy nginx-demo --image=nginx --dry-run=client -o yaml > new-deploy.yaml
```

## Inspecting a Deployment

List all Deployments in the current namespace:

```bash
kubectl get deploy
```

List all related resources (Deployment, ReplicaSet, and Pods):

```bash
kubectl get all
```

View detailed information about the `nginx-deployment`:

```bash
kubectl describe deploy nginx-deployment
```

## Updating a Deployment

Update the container image for the `nginx` container in the `nginx-deployment` to `nginx:1.9.1`:

```bash
kubectl set image deploy/nginx-deployment nginx=nginx:1.9.1
```

## Scaling a Deployment

Scale the `nginx-deployment` to 10 replicas:

```bash
kubectl scale deployment/nginx-deployment --replicas=10
```

## Managing Deployment Rollouts

### View Rollout History

Check the rollout history of the `nginx-deployment`:

```bash
kubectl rollout history deploy/nginx-deployment
```

View details of a specific revision (e.g., revision 2):

```bash
kubectl rollout history deployment/nginx-deployment --revision=2
```

### Check Rollout Status

Monitor the status of the `nginx-deployment` rollout:

```bash
kubectl rollout status deployment/nginx-deployment
```

### Rollback a Deployment

Undo the latest rollout to revert to the previous revision:

```bash
kubectl rollout undo deploy/nginx-deployment
```

Rollback to a specific revision (e.g., revision 2):

```bash
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```

## Deleting a Deployment

Delete the `nginx-deployment` (this will also terminate the managed ReplicaSets and pods):

```bash
kubectl delete deploy nginx-deployment
```

## Notes

- The `kubectl apply` command is idempotent and preferred for declarative management of resources.
- The `selector.matchLabels` in the Deployment must match the pod template's `labels` to manage the pods correctly.
- Deployments automatically create and manage ReplicaSets to handle pod replication and updates.
- Use `kubectl set image` for quick image updates or `kubectl edit` for broader configuration changes.
- The `kubectl rollout` commands provide powerful tools for managing updates and rollbacks, ensuring minimal downtime.
- **Caution**: Ensure bare pods do not have labels that match the Deployment’s selector to prevent unintended pod adoption by the underlying ReplicaSet.

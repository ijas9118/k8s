# Kubernetes ReplicaSet Management

This README provides a guide to managing Kubernetes ReplicaSets using `kubectl` commands. It covers creating, inspecting, scaling, and deleting a ReplicaSet, as well as important considerations for managing pods with selectors. ReplicaSets are part of the `apps/v1` API group and are used to ensure a specified number of pod replicas are running.

## Prerequisites
- A running Kubernetes cluster
- `kubectl` installed and configured to interact with your cluster

## Understanding ReplicaSet
To view the schema and details of a ReplicaSet resource:

```bash
kubectl explain rs
```

**API Group Details**:
- **Group**: apps
- **Kind**: ReplicaSet
- **Version**: v1

## Creating a ReplicaSet
### Using YAML Configuration
Below is a sample YAML configuration for a ReplicaSet named `frontend` that ensures 3 replicas of an `nginx` pod are running:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
```

Save the above configuration as `rs.yaml` and apply it to create the ReplicaSet:

```bash
kubectl apply -f rs.yaml
```

## Inspecting a ReplicaSet
List all ReplicaSets in the current namespace:

```bash
kubectl get rs
```

View detailed information about the `frontend` ReplicaSet:

```bash
kubectl describe rs/frontend
```

## Managing Pods with Selectors
A ReplicaSet can manage pods that match its `selector` (`matchLabels`), even if those pods were not created by the ReplicaSet's template. **Important**: Ensure bare pods do not have labels that match the ReplicaSet's selector to avoid unintended acquisition. Pods acquired by a ReplicaSet will be managed by it, and if they lack a controller (owner reference), they will be adopted immediately.

## Editing a ReplicaSet
Modify the `frontend` ReplicaSet to change the number of replicas (e.g., to 5):

```bash
kubectl edit rs/frontend
```

In the editor, update the `spec.replicas` field to `5`. Upon saving, Kubernetes will instantly create two additional pods to meet the new replica count.

## Scaling a ReplicaSet
Scale the `frontend` ReplicaSet to 2 replicas:

```bash
kubectl scale --replicas=2 rs/frontend
```

Scale conditionally, only if the current replica count is 2, to 4 replicas:

```bash
kubectl scale --current-replicas=2 --replicas=4 rs/frontend
```

## Deleting a ReplicaSet
Delete the `frontend` ReplicaSet (this will also terminate the managed pods unless they have other controllers):

```bash
kubectl delete rs frontend
```

## Notes
- The `kubectl apply` command is idempotent and preferred for declarative management of resources.
- The `selector.matchLabels` in the ReplicaSet must match the pod template's `labels` to manage the pods correctly.
- Use `kubectl scale` for quick replica adjustments or `kubectl edit` for broader configuration changes.
- ReplicaSets are often managed by Deployments in modern Kubernetes setups for rolling updates and rollback capabilities.
- **Caution**: Avoid creating bare pods with labels that match a ReplicaSet’s selector to prevent unintended pod adoption.
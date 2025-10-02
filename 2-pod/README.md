# Kubernetes Pod Management

This README provides a concise guide to managing Kubernetes pods using `kubectl` commands. It includes examples for creating, inspecting, editing, and deleting pods, both imperatively and declaratively using a YAML configuration.

## Prerequisites

- A running Kubernetes cluster
- `kubectl` installed and configured to interact with your cluster

## Creating a Pod

### Using Imperative Command

Create a pod named `nginx-pod` with the `nginx:latest` image:

```bash
kubectl run nginx-pod --image=nginx:latest
```

Verify the pod creation:

```bash
kubectl get pods
```

### Using YAML Configuration

Generate a YAML configuration file for the pod:

```bash
kubectl run nginx-pod --image=nginx --dry-run=client -o yaml > config.yaml
```

Create or apply the pod using the YAML file:

```bash
kubectl create -f config.yaml
```

or

```bash
kubectl apply -f config.yaml
```

- Sample yaml for pod:

  ```yml
  apiVersion: v1
  kind: Pod
  metadata:
  name: nginx-pod
  labels:
    app: demo
  spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
  ```

## Inspecting a Pod

View detailed information about the `nginx-pod`:

```bash
kubectl describe pod nginx-pod
```

## Editing a Pod

Modify the `nginx-pod` configuration directly:

```bash
kubectl edit pod nginx-pod
```

## Deleting a Pod

Remove the `nginx-pod`:

```bash
kubectl delete pod nginx-pod
```

## Notes

- The `kubectl apply` command is idempotent and preferred for managing resources declaratively.
- Use `--dry-run=client` to preview the YAML configuration without creating resources.
- Ensure the YAML file (`config.yaml`) is stored in the appropriate directory (e.g., `2-pod/`) for easy reference.

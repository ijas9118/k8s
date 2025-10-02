# Kubernetes Namespace Management

This README provides a guide to managing Kubernetes Namespaces using `kubectl` commands. It covers creating, inspecting, and deleting Namespaces, as well as managing Deployments, Pods, and Services within them. It includes specific tasks for creating two Namespaces (`ns1` and `ns2`), deploying applications, testing connectivity between pods and services, and handling DNS resolution using Fully Qualified Domain Names (FQDNs).

## Prerequisites

- A running Kubernetes cluster
- `kubectl` installed and configured to interact with your cluster

## Understanding Namespaces

Namespaces provide a way to partition resources within a Kubernetes cluster, enabling isolation and organization.

To view the schema and details of a Namespace resource:

```bash
kubectl explain ns
```

List all Namespaces in the cluster:

```bash
kubectl get ns
```

## Inspecting Resources in Namespaces

View all resources in the `kube-system` Namespace:

```bash
kubectl get all --namespace=kube-system
```

View all resources in the `default` Namespace:

```bash
kubectl get all -n default
```

## Creating a Namespace

### Using YAML Configuration

**Example YAML** (`namespace.yaml`):

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: demo
```

Apply the Namespace:

```bash
kubectl apply -f namespace.yaml
```

### Using Imperative Command

Create a Namespace named `demo`:

```bash
kubectl create ns demo
```

## Creating a Deployment in a Namespace

Create a Deployment named `nginx-demo` in the `demo` Namespace:

```bash
kubectl create deploy nginx-demo --image=nginx -n demo
```

Create a Deployment named `nginx-test` in the `default` Namespace:

```bash
kubectl create deploy nginx-test --image=nginx
```

List all resources in the `demo` Namespace:

```bash
kubectl get all -n demo
```

## Accessing Pods

Execute a shell session in a pod in the `default` Namespace (replace pod name with actual):

```bash
kubectl exec -it pod/nginx-test-b548755db-822tn -- sh
```

Execute a shell session in a pod in the `demo` Namespace (replace pod name with actual):

```bash
kubectl exec -it pod/nginx-demo-87cd4cbb7-9lhhg -n demo -- sh
```

## Scaling Deployments

Scale the `nginx-demo` Deployment to 3 replicas in the `demo` Namespace:

```bash
kubectl scale deploy/nginx-demo --replicas=3 -n demo
```

Scale the `nginx-test` Deployment to 3 replicas in the `default` Namespace:

```bash
kubectl scale deploy/nginx-test --replicas=3
```

## Exposing Deployments as Services

Expose the `nginx-demo` Deployment as a Service named `svc-demo` in the `demo` Namespace:

```bash
kubectl expose deploy/nginx-demo --name=svc-demo --port=80 -n demo
```

Expose the `nginx-test` Deployment as a Service named `svc-test` in the `default` Namespace:

```bash
kubectl expose deploy/nginx-test --name=svc-test --port=80
```

## Testing Connectivity

### Pod-to-Pod Connectivity Across Namespaces

Pods can communicate across Namespaces using pod IPs. Get the pod IPs:

```bash
kubectl get pods -n demo -o wide
kubectl get pods -n default -o wide
```

From a pod in the `default` Namespace, `curl` the IP of a pod in the `demo` Namespace:

```bash
kubectl exec -it pod/nginx-test-b548755db-822tn -- curl <demo-pod-ip>
```

This should return a successful response (e.g., nginx welcome page).

### Service Connectivity Across Namespaces

Attempt to `curl` the Service in the `demo` Namespace from a pod in the `default` Namespace using the Service name:

```bash
kubectl exec -it pod/nginx-test-b548755db-822tn -- curl svc-demo
```

This will fail with a "Could not resolve host" error because the Service name is not resolvable across Namespaces without the FQDN.

Check the DNS configuration inside a pod:

```bash
kubectl exec -it pod/nginx-test-b548755db-822tn -- cat /etc/resolv.conf
```

Use the Fully Qualified Domain Name (FQDN) to access the Service:

```bash
kubectl exec -it pod/nginx-test-b548755db-822tn -- curl svc-demo.demo.svc.cluster.local
```

This should return a successful response. Similarly, from a pod in the `demo` Namespace, access the `svc-test` Service in the `default` Namespace:

```bash
kubectl exec -it pod/nginx-demo-87cd4cbb7-9lhhg -n demo -- curl svc-test.default.svc.cluster.local
```

## Deleting a Namespace

Delete the `demo` Namespace (this will also delete all resources within it):

```bash
kubectl delete ns demo
```

## Notes

- Namespaces isolate resources, but pods can communicate across Namespaces using pod IPs or Service FQDNs.
- The `kubectl apply` command is idempotent and preferred for declarative management.
- Use `-n <namespace>` to specify the Namespace for commands, or omit for the `default` Namespace.
- The FQDN format for Services is `<service-name>.<namespace>.svc.cluster.local`.
- **Caution**: Deleting a Namespace removes all resources within it, so ensure no critical resources are present before deletion.

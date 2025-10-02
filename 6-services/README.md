# Kubernetes Service Management

This README provides a guide to managing Kubernetes Services using `kubectl` commands. It covers creating and inspecting different Service types (NodePort, ClusterIP, LoadBalancer, and ExternalName), along with completing specific tasks related to a `myapp` Service and Deployment. It also discusses key considerations for exposing pods and choosing appropriate Service types.

## Prerequisites
- A running Kubernetes cluster
- `kubectl` installed and configured to interact with your cluster
- Pods labeled with `app: nginx` or as specified (e.g., from a Deployment)

## Understanding Services
To view the schema and details of a Service resource:

```bash
kubectl explain svc
```

List pods with their labels to identify selectors for Services:

```bash
kubectl get pods --show-labels
```

## Service Types
### 1. NodePort Service
Exposes the Service on a specific port of each node in the cluster.

**Example YAML** (`nodeport.yaml`):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-svc
  labels:
    app: demo
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30000
  selector:
    app: nginx
```

Apply the NodePort Service:

```bash
kubectl apply -f nodeport.yaml
```

Inspect the Service:

```bash
kubectl describe svc nodeport-svc
```

Access the Service using `<node-ip>:30000` (replace `<node-ip>` with the IP of a cluster node). For example:

```bash
curl http://<node-ip>:30000
```

View pod details (e.g., for a pod from `nginx-deployment`):

```bash
kubectl describe pod/nginx-deployment-6f9664446b-265zp
```

List Endpoints associated with the Service:

```bash
kubectl get ep
```

### 2. ClusterIP Service
Exposes the Service internally within the cluster (default type).

**Example YAML** (`clusterip.yaml`):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: clusterip-svc
  labels:
    app: demo
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
  selector:
    app: nginx
```

Apply the ClusterIP Service:

```bash
kubectl apply -f clusterip.yaml
```

## Tasks
### Task 1: Create a ClusterIP Service Named `myapp`
Create a Service named `myapp` of type ClusterIP that exposes port 80 and maps to target port 80.

**YAML** (`myapp-service.yaml`):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
  selector:
    app: myapp
```

Apply the Service:

```bash
kubectl apply -f myapp-service.yaml
```

### Task 2: Create a Deployment Named `myapp`
Create a Deployment named `myapp` with 1 replica running the `nginx:1.23.4-alpine` image, exposing container port 80.

**YAML** (`myapp-deployment.yaml`):
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.4-alpine
        ports:
        - containerPort: 80
```

Apply the Deployment:

```bash
kubectl apply -f myapp-deployment.yaml
```

### Task 3: Scale the Deployment to 2 Replicas
Scale the `myapp` Deployment to 2 replicas:

```bash
kubectl scale deployment/myapp --replicas=2
```

### Task 4: Run a `wget` Command from a Temporary Pod
Create a temporary pod using the `busybox` image and run a `wget` command against the ClusterIP of the `myapp` Service.

First, get the ClusterIP of the `myapp` Service:

```bash
kubectl get svc myapp
```

Run the temporary pod and execute `wget`:

```bash
kubectl run busybox --image=busybox --rm -it -- sh -c "wget -O- http://myapp"
```

### Task 5: Run a `wget` Command Against the Service Outside the Cluster
Since ClusterIP Services are internal to the cluster, they are not accessible outside. Attempting to run `wget` against the ClusterIP from outside the cluster will fail:

```bash
wget http://<cluster-ip>
```

This will result in a connection error because ClusterIP is not externally routable.

### Task 6: Change the Service Type to NodePort
Modify the `myapp` Service to type `NodePort` to allow external access.

Edit the Service:

```bash
kubectl edit svc myapp
```

Change `spec.type` to `NodePort` and optionally specify a `nodePort` (e.g., 30001) under `spec.ports`:

```yaml
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30001
  selector:
    app: myapp
```

Apply the updated Service (if using a YAML file):

```bash
kubectl apply -f myapp-service.yaml
```

### Task 7: Run a `wget` Command Against the NodePort Service
Get the node IP and use the assigned `nodePort` (e.g., 30001):

```bash
kubectl get nodes -o wide
```

Run `wget` from outside the cluster:

```bash
wget http://<node-ip>:30001
```

### Task 8: Exposing Pods as a Service Without a Deployment
**Discussion**: Yes, pods can be exposed as a Service without a Deployment. A Service can target any pods that match its `selector`, regardless of whether they are managed by a Deployment, ReplicaSet, or created as bare pods. For example:

1. Create a bare pod with a label (e.g., `app: myapp`).
2. Create a Service with a `selector` matching the pod’s label.

**Example**:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: nginx
    image: nginx:1.23.4-alpine
    ports:
    - containerPort: 80
```

Apply a Service to expose the pod:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: myapp
```

**Caution**: Without a Deployment or ReplicaSet, Kubernetes will not automatically manage pod replicas or restarts. If the pod fails, the Service will have no pods to route traffic to until a new pod with matching labels is created.

### Task 9: When to Use Different Service Types
**Discussion**:
- **ClusterIP**:
  - **Use Case**: Internal communication within the cluster (e.g., between microservices).
  - **Condition**: When pods need to communicate with each other inside the cluster without external exposure.
  - **Example**: A backend service communicating with a database pod.

- **NodePort**:
  - **Use Case**: Exposing a Service to external traffic on a specific port of each node.
  - **Condition**: When you need simple external access for testing or in clusters without a cloud provider’s LoadBalancer support.
  - **Example**: Accessing a web application during development.

- **LoadBalancer**:
  - **Use Case**: Exposing a Service to external traffic with a cloud provider’s load balancer.
  - **Condition**: When running in a cloud environment (e.g., AWS, GCP, Azure) that supports LoadBalancer integration for production-grade external access.
  - **Example**: Public-facing APIs or web applications.

- **ExternalName**:
  - **Use Case**: Mapping a Kubernetes Service to an external DNS name without creating a local proxy.
  - **Condition**: When you need to redirect traffic to an external service (e.g., an external database or API) using a Kubernetes Service name.
  - **Example**: Accessing an external payment gateway via a Service named `payment-api`.

**Example ExternalName YAML** (`externalname.yaml`):
```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-svc
spec:
  type: ExternalName
  externalName: example.com
```

## Notes
- The `kubectl apply` command is idempotent and preferred for declarative management.
- Ensure the `selector` in the Service matches the pod labels to route traffic correctly.
- Use `kubectl get ep` to verify the Endpoints associated with a Service.
- When changing Service types, ensure compatibility with your cluster’s networking setup (e.g., LoadBalancer requires cloud provider support).
- **Caution**: Bare pods exposed by a Service lack automatic scaling or recovery, making Deployments or ReplicaSets preferable for production.
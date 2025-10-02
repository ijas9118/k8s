![Kubernetes](https://img.shields.io/badge/Kubernetes-Declarative%20Orchestration-blue?style=plastic&logo=kubernetes&logoColor=white)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# Kubernetes Learning Repository

Welcome to the **Kubernetes (k8s) Learning Repository**! This repository is a comprehensive, hands-on guide to mastering Kubernetes fundamentals through practical examples, commands, and configurations.

## Quick Links

[![Pods](https://img.shields.io/badge/Pods-Guide-blue?style=for-the-badge&logo=kubernetes&logoColor=white)](./2-pod)
[![ReplicationController](https://img.shields.io/badge/ReplicationController-Guide-green?style=for-the-badge&logo=kubernetes&logoColor=white)](./3-replicationcontroller)
[![ReplicaSet](https://img.shields.io/badge/ReplicaSet-Guide-purple?style=for-the-badge&logo=kubernetes&logoColor=white)](./4-replicaset)
[![Deployments](https://img.shields.io/badge/Deployments-Guide-orange?style=for-the-badge&logo=kubernetes&logoColor=white)](./5-deployment)
[![Services](https://img.shields.io/badge/Services-Guide-red?style=for-the-badge&logo=kubernetes&logoColor=white)](./6-services)
[![Namespaces](https://img.shields.io/badge/Namespaces-Guide-yellow?style=for-the-badge&logo=kubernetes&logoColor=white)](./7-namespaces)

_Note: More sections on advanced topics like ConfigMaps, Secrets, Ingress, Helm, and more will be added soon!_

## What This Repository Contains

This repository is organized into topic-specific directories, each containing:

- **Detailed README.md files**: Step-by-step guides with `kubectl` commands, YAML configurations, and explanations.
- **Sample YAML files**: Ready-to-use configuration files for resources like pods, deployments, services, etc.
- **Best Practices and Notes**: Tips, cautions, and discussions on when to use specific resources.

The content focuses on core Kubernetes concepts, using `nginx` as a common example application for simplicity. All examples assume a running Kubernetes cluster (e.g., Minikube, Kind, or a cloud-based cluster) and `kubectl` configured for access.

## How to Use This Repository

1. **Setup Your Environment**:

   - Install Kubernetes (e.g., via [Minikube](https://minikube.sigs.k8s.io/docs/start/), or [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/) for local development).
   - Ensure `kubectl` is installed and connected to your cluster: `kubectl cluster-info`.
   - Clone this repository: `git clone https://github.com/ijas9118/k8s.git`.

2. **Navigate and Learn**:

   - Start with the [Pods](pods/) section for the basics.
   - Follow the Table of Contents to progress through topics.
   - Read each README.md for theory, commands, and YAML examples.
   - Copy YAML files to your local environment and apply them with `kubectl apply -f <file.yaml>`.

3. **Hands-On Practice**:

   - Run the provided `kubectl` commands in your terminal.
   - Complete the tasks in each section to build and test resources.
   - Experiment with modifications (e.g., scaling replicas, changing images) to deepen understanding.
   - Use `kubectl describe` and `kubectl logs` for debugging.

4. **Cleanup**:

   - Delete resources after testing: e.g., `kubectl delete -f <file.yaml>` or `kubectl delete ns <namespace>`.
   - For local clusters, reset with `minikube delete` if needed.

5. **Contribute**:
   - Found an error? Have suggestions? Fork the repo, make changes, and submit a Pull Request.
   - Feel free to add new sections or examples!

## Prerequisites

- Basic knowledge of Docker/containers.
- Familiarity with YAML and command-line tools.
- A Kubernetes cluster (local or remote).

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

⭐ If this repository helps you, please star it on GitHub! Contributions and feedback are welcome.

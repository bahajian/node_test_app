# Node Test App
---


## Getting started


This is a simple Node.js application that can be deployed using Docker, Kubernetes, or directly in a Node.js environment.

### Deployment Options
You can deploy this application using one of the following methods:

- Docker: A GitHub Actions CI workflow is included to automatically build the Docker image and push it to a Docker Hub repository.
- Kubernetes: You can deploy this application in a Kubernetes cluster   using one of the following approaches:
  - Ingress and NodePort
  - MetalLB (LoadBalancer)

#### Kubernetes Deployment Instructions
Each deployment method includes detailed instructions to help you set up the application in a Kubernetes cluster.

Additionally, there are guidelines on how to integrate ArgoCD into your cluster for GitOps-based deployment.


# Kubernetes Deployment with NFS and HTTP Server

## Dockerfile

- The Dockerfile sets up an Nginx server to serve static content.
- The server is configured to serve the `hello.html` file stored on an NFS volume.
- **Image Size**: The built Docker image (`my-http-server`) is approximately **440 MB**.


## Kubernetes Files
1. **nfs-pvc.yaml** - Defines a Persistent Volume Claim (PVC) for the NFS storage.
2. **http-deployment.yaml** - Deploys an HTTP server with NFS volume mounted to serve files.
3. **http-service.yaml** - Exposes the HTTP server as a NodePort service.
4. **content-copy-job.yaml** - A job that writes content to the NFS volume for the HTTP server to serve.

## Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/docs/start/) installed with Docker as the driver.
- [Docker](https://docs.docker.com/get-docker/) installed.
- [Helm](https://helm.sh/docs/intro/install/) installed (for NFS provisioner setup).
  
## Instructions to Run

```bash
# Start Minikube
minikube start --driver=docker

# Configure Docker to Use Minikube’s Docker Daemon
eval $(minikube docker-env)

# Install Helm (if not already installed)
brew install helm

# Add the Helm Stable Repository and Install NFS Server Provisioner
helm repo add stable https://charts.helm.sh/stable
helm repo update
helm install nfs-server stable/nfs-server-provisioner --set storageClass.name=nfs-storage

# Build the Docker Image for the HTTP Server
cd path/to/project-directory
docker build -t my-http-server .

# Apply Kubernetes Configurations
kubectl apply -f nfs-pvc.yaml           # Persistent Volume Claim
kubectl apply -f http-deployment.yaml    # HTTP Server Deployment
kubectl apply -f http-service.yaml       # Service to expose the HTTP server
kubectl apply -f content-copy-job.yaml   # Job to write content to NFS volume

# Run `minikube tunnel` in a separate terminal (keep this terminal open)
minikube tunnel

# Get the Service URL (run in the main terminal)
minikube service http-service --url

# Optional: Check the Status of Pods and Services
kubectl get pods      # List all running pods
kubectl get svc       # List all services


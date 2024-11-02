# Large-Scale-Computing

# Kubernetes Deployment with NFS and HTTP Server

## Dockerfile
- The Dockerfile sets up an Nginx server to serve static content.
- The server is configured to serve the `hello.html` file stored on an NFS volume.

## Kubernetes Files
1. **nfs-pvc.yaml** - Defines a Persistent Volume Claim (PVC) for the NFS storage.
2. **http-deployment.yaml** - Deploys an HTTP server with NFS volume mounted to serve files.
3. **http-service.yaml** - Exposes the HTTP server as a NodePort service.
4. **content-copy-job.yaml** - A job that writes content to the NFS volume for the HTTP server to serve.

## Commands Used for Deployment

```bash
# Start Minikube
minikube start --driver=docker

# Install Helm and add NFS chart
brew install helm
helm repo add stable https://charts.helm.sh/stable
helm install nfs-server stable/nfs-server-provisioner --set storageClass.name=nfs-storage

# Apply Kubernetes configurations
kubectl apply -f nfs-pvc.yaml
kubectl apply -f http-deployment.yaml
kubectl apply -f http-service.yaml
kubectl apply -f content-copy-job.yaml

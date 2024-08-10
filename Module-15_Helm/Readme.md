# Manage K8s Application Deployment using Helm

## Step-01: Install Helm

- https://helm.sh/docs/intro/install/

- [As a pre-requisite, you can download and install chocolatey package manager from https://chocolatey.org/install#individual]

```
# MacOS
brew install helm

# Using Chocolatey (Windows)
choco install kubernetes-helm

# Using Scoop (Windows)
scoop install helm

# Verify Helm version
helm version
```

## Step-02: List, Add and Search Helm Repository

- Search for Helm Charts at Artifacthub (https://artifacthub.io/)

```
# List Helm Repositories
helm repo list

# Add Helm Repository
helm repo add <desired_name> <helm_repo_url>
helm repo add mybitnami https://charts.bitnami.com/bitnami

# List Helm repositories
helm repo list

# Search any Helm repository
helm search repo <repo_name>
helm search repo nginx
helm search repo apache
helm search repo apache <version>
```

## Step-03: Install Helm Charts

```
# Update Helm Repo | Get the latest list of charts
helm repo update

# Install Helm Chart
helm install <name> <repo_name/chart_name>
helm install bitnami mybitnami/nginx
```

## Step-04: List Helm Releases

```
# List Helm Releases (Default Table Output)
helm list
helm ls

# List Helm Releases
helm list --output=yaml

# List Helm Releases
helm list --output=json

# List Helm Releases with namespace flag
helm list --namespace=default
helm list -n default
```

## Step-05: List K8s resources to verify the app deployment

```
# List Kubernetes Pods
kubectl get pods

# List Kubernetes Services
kubectl get svc

[Review the EXTERNAL-IP field and you will see it as localhost. Access the nginx page]

# Access Nginx Application on local desktop browser
http://localhost_ec2_instance_ip:80
http://127.0.0.1:80

# Access Application using curl command
curl http://localhost:80
curl http://127.0.0.1:80
```

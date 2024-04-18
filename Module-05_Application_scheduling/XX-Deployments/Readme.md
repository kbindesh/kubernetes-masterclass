# Deploying and managing applications

- A **Deployment** is a kubernetes object which provides us a convenient way to define the desired state application deployment.
- It provides us with a better way of upgrading the underlying instances seamlessly using:

  - rolling updates
  - undoing changes, and
  - pausing and resuming changes as required

- Deployments provide a way to define a desired state for the replica pod.

### Step-01: Create a Deployment in declarative way

- Write a YAML definition for Deployment object:

```
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

The following attributes are important to help you understand the preceding YAML definition:

- spec.replicas gives us the number of replica pods
- spec.template is the template pod descriptor that defines the pods that will be created
- spec.selector is the deployment that will manage all pods whose labels match this selector

### Step-02: Deploy the application using Deployment object

```
kubectl apply -f deployment.yaml
```

### Step-03: Get the deployment listing

```
kubectl get deployments
```

### Step-04: Describe deployment details

```
# Syntax
kubectl describe deployment <deployment_name>

# Example
kubectl describe deployment deployment.yaml
```

### Step-05: Live edit the deployments

```
kubectl edit deployment <deployment_name>

# You may revise the replica count or image then save and quit using wq!
```

### Step-06: Delete Deployments

```
kubectl delete deployment <deployment_name>
```

### Step-07: Performing rolling updates on Deployment

- Rolling updates provide a way to update a Deployment to a newer version more effectively and efficiently.
- This way, you can update Kubernetes objects such as replicas and pods gradually with nearly zero downtime.

- **Rolling updates in imperative way**

```
# Create a new kubernetes deployment
kubectl create deployment <deployment_name> --image=<image_name>:<tag>

kubectl create deployment app-deployment --image=nginx:latest

# Update the container image
kubectl set image deployment/app-deployment nginx=nginx:1.25.5 --record

# --record flag records information about the updates so that it can be rolled back later.

# You can either use --record flag or --record=true flag.

# Describe deployment to check the image update
kubectl describe deployment deployment.yaml
```

- **Rolling updates in declarative way**

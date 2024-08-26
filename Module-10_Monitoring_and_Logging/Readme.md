# Monitoring and Logging Kubernetes Clusters and Apps

In this module, you will learn following:

- How to monitor Kubernetes cluster components and applications?
- How to get the infrastructure-level, system-level, and application-level logs to serve as a source for log analytics or troubleshooting?
- Monitoring applications on a Kubernetes cluster
- Managing logs at the cluster node and pod levels
- Managing container **stdout** and **stderr** logs

## 01. Monitoring on a cluster node

### Step-1.1: Check whether _Metrics Server_ is installed or not

```
# List all the worker nodes in the cluster
kubectl get nodes

[The preceding command will list all the worker node names. Here, let's assume node name is "minikube"]

# Check the metrics for the worker node (e.g. minikube)
kubectl top node minikube

[ The output of this command will show the resource usage of the minikube node if you have Metrics Server installed]
```

- In case if the Metrics server is not installed on your K8s server, you will the following error

```
error: Metrics API not available
```

### Step-1.2: Installing Metrics Server on Kubernetes cluster

- To get the latest release and instructions, you can go to kubernetes gitHub repo: https://github.com/kubernetes-sigs/metrics-server .

#### 1.2.1 Using minikube Addon (on minikube distro)

```
# List minikube addons
minikube addons list

# List minikube addons and filter the metrics-server
minikube addons list | grep metrics-server

# Enable metrics-server addon on minikube k8s cluster
minikube addons enable metrics-server

# List the pods and services related to metrics-server
kubectl get pods,svc -n kube-system
OR
kubectl get pods -n kube-system | grep metrics-server
```

#### 1.2.2 Using a YAML manifest file

```
# Deploy Metrics server using the official YAML manifest file
kubectl apply -f https://github.com/kubernetes-sigs/metricsserver/releases/latest/download/components.yaml
```

- There is also a high-availability (HA) version that increases the replica count from one to two for Metrics Server.
- If you have a k8s cluster with at least two nodes, you can use the following manifest file for installing metrics-server

```
kubectl apply -f https://github.com/kubernetes-sigs/metricsserver/releases/latest/download/high-availability.yaml
```

- To get more information about Metrics Server, follow this link: https://github.com/kubernetessigs/metrics-server/releases

#### 1.2.3 Using Helm Charts

- To install Metrics Server using Helm charts, you can go to [Artifact Hub](https://artifacthub.io/) and then find the Metrics Server Helm charts at https://artifacthub.io/packages/helm/metrics-server/metrics-server

```
# Add helm repo for metrics-server
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/

# Install the Helm charts
helm upgrade --install metrics-server metrics-server/metricsserver
```

## 02. Checking out CPU and Memory metrics

- To get the CPU and memory metrics details of any worker node

```
# Syntax
kubectl top node <NODE_NAME>

# Example
kubectl top node minikube
```

## 03. Monitoring Applications on a Kubernetes cluster

- In Kubernetes, the **metrics-server** is not only used to monitor the Kubernetes worker nodes but also the _pods_ and _containers_.

### Step-3.1: Monitoring the resource usage of an application

```
# Deploy a test application Pod to monitor it using metrics-server
kubectl run nginx --image=nginx:latest

# Check the resource utilization of the above Pod
kubectl top pod nginx

# Check the resource utilization of multi-container pods
kubectl top pod <POD_NAME> --containers
```

- To show all the metrics of all the Pods across different namespaces, use following command:

```
kubectl top pod -A

OR

kubectl top pod --all-namespaces
```

- List the Pods with it's memory and cpu resource utilization details in sorted form

```
kubectl top pod --sort-by=cpu

kubectl top pod –-sort-by=memory

[This can help you in figuring out the pods with most/least resource consumption]
```

### Step-3.2: Checking application details

```
# Describe a Pod from default namespace
kubectl describe pod <POD_NAME>

# Describe a Pod present in a custom namespace
kubectl describe pod <POD_NAME> -n <NAMESPACE>
```

### Step-3.3: Monitor K8s cluster events

```
kubectl get events

# List the events sorted by timestamp
kubectl get events --sort-by=.metadata.creationTimestamp

# To collect the events during a deployment | Real-time logs
kubectl get events --watch
```

## 4. Managing logs at the K8s Cluster Node and Pod levels

### 4.1 Node level details

```
# Get more details about a node
kubectl describe node <NODE_NAME>

# Checking node status
kubectl get node minikube -o wide
```

## 5. Managing container `stdout`, `stdin` and `stderr` logs

```
kubectl logs <POD_NAME>
```

- We can get into the pod to retrieve the specific container log by using the **-c flag**.

```
kubectl logs <POD_NAME> -c <CONTAINER_NAME>
```

- Stream the logs

```
kubectl logs -f <POD_NAME>
```

- List logs generated since xx duration

```
# Since last 1 hour
kubectl logs --since=1h
```

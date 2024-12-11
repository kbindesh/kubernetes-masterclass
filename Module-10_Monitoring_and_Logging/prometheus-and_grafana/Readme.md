# Monitoring Amazon EKS cluster using Prometheus & Grafana

## Prerequisites

- An Amazon EKS cluster
- kubectl
- eksctl

## Reference links

- [Prometheus Documentation](https://prometheus.io/docs/introduction/overview/)
- [Grafana Documentation](https://grafana.com/)

## Step-01: [Install Helm](https://github.com/kbindesh/kubernetes-masterclass/tree/main/Module-15_Helm)

## Step-02: Setup Prometheus

### 2.1: Create a K8s namespace for Prometheus

- Connect to your EKS cluster and create a dedicated namespace:

```
kubectl create namespace monitoring
```

### 2.2: Add Helm repo

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

### 2.3: Update the Helm chart repo

```
helm repo update
helm repo list
```

### 2.4: Install Prometheus using Helm

```
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring
```

### 2.5: Update the Prometheus service to LoadBalancer

```
kubectl edit svc prometheus-kube-prometheus-prometheus -n monitoring
```

### 2.6: Access the Prometheus Dashboard using Amazon ELB

```
https://<elb_endpoint>:9090
```

## Step-03: Connect to Prometheus via Dashboard (UI)

## Step-04: Check Node executor for EKS Cluster monitoring

## Step-05: Configure Grafana Dashboard

### 5.1: Change the grafana service type to Loadbalancer

```
kubectl edit svc prometheus-grafana
```

### 5.2: Access + Login Grafana dashboard

```
# Use the below username and password

username: admin
password: prom-operator
```

### 5.3 Check the following

- "Node Exporter/USE method/Node"
- "Node Exporter/USE method/Cluster" USE - Utilization, Saturation, Errors
- Behavior of each pod, node, and cluster

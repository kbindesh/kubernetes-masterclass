# Seting up Azure Kubernetes Service (EKS) Cluster (using portal)

- In this section, we will learn how to setup an an AKS cluster, a Production-grade Kubernetes distribution.
- In order to setup the EKS cluster, we will perform following steps:
  - **Install Azure CLI**
  - **Install kubectl CLI**
  - **Create and configure AKS Cluster**
  - **Access the AKS cluster**

## Step-01: Create a Resource Group

## Step-02: Create an AKS Cluster using Azure portal

### Basic

- Subscription: <subscription_name>
- Resource Group: <resource_group_name>
- Cluster preset configuration: dev/test
- Kubernetes cluster name: bin-aks-cluster
- Region: East US
- Availability Zones: Zones 1,2,3
- AKS pricing tier: Free
- Kubernetes version: 1.28.9(default)
- Automatic upgrade: Disable
- Node security channel upgrade:
- Authentication and Authorization: Local account with Kubernetes RBAC

### Node pools

- Node size: D2sv3
- OS SKU: Ubuntu
- Scale method: Autoscale
- Node count: 2-5
- Availability zone: Zones 1,2,3
- Max pods/node:

### Networking

- Network configuration: Azure CNI
- Virtual Network: <create_new_vnet>
- Network Policy: None

### Integrations

### Monitoring

### Advanced

- Create a new infrastructure resource group

### Tags

### Review & Create

## Step-03: Install Azure CLI

## Step-04: Install kubectl CLI

## Step-05: Access the AKS cluster

## Step-06: Deploy application on AKS cluster

# Introducing Kubernetes

In this module, we're going to learn the following topics:

- **What is Kubernetes?**
- **Understanding container orchestration**
- **Understading Kubernetes key concepts**
- **Kubernetes container runtimes**
- **Kubernetes Distributions**

## 01. What is Kubernetes?

- Kubernetes is a platform to orchestrate the deployment, scaling, and management of container-based applications.
- The correct pronunciation of Kubernetes is **Koo-ber-netties** or **Koo-ber-nay-tace**.
- Kubernetes was a Google project, but joined the _Cloud Native Computing Foundation (CNCF)_ and is now the de facto standard in the space of container-based applications.
- The core functionality is scheduling workloads in containers across your infrastructure.
- Some of the other kubernetes capabilites are as follows:
  - **Providing authentication and authorization**
  - **Debugging applications**
  - **Accessing and ingesting logs**
  - **Rolling updates**
  - **Using Cluster Autoscaling**
  - **Using the Horizontal Pod Autoscaler**
  - **Replicating application instances**
  - **Checking application health and readiness**
  - **Monitoring resources**
  - **Balancing loads**
  - **Naming and service discovery**
  - **Distributing secrets**
  - **Mounting storage systems**

## 02. Understanding container orchestration

- The primary responsibility of Kubernetes is container orchestration.
- K8s make sure that all the containers that execute various workloads are scheduled to run on physical or virtual machines.
- Kubernetes must keep an eye on all running containers and replace dead, unresponsive, or unhealthy containers.
- In order to run workloads, you need:
  - Physical machines
  - Virtual machines (optional)
  - Local persistent disks
  - Shared persistent disks
  - Networking resources
- Kubernetes can be deployed on a bare-metal cluster or on a cluster of virtual machines.
- Kubernetes in turn can orchestrate the containers it manages directly on bare-metal or on virtual machines.

### Advantages of Containers

Here are some of the benefits of containers compared to more conventional software packaging, shipping, deployment and ops models:

- Agile application creation and deployment
- Continuous development, integration, and deployment
- Development and operations separation of concerns
- Environmental consistency across development, testing, staging, and production
- Cloud and OS distribution portability
- Application-centric management
- Resource isolation
- Resource utilization

### Microservices & Containers in the Cloud

- Microservices are the widely used architecture for modern large-scale systems.
- The primary idea with microservice is to break down the system into small services with well-defined responsibilities that manage their own data and communicate with other microservices through well-defined APIs.
- Containers are ideal to package microservices because, while providing isolation to the microservice.
- Containers are very lightweight and you do not incur a lot of overhead when deploying many microservices, as you do with virtual machines.
- All major cloud providers, such as AWS, Google Cloud Platform, and Microsoft Azure, provide container hosting services, like:
  - Amazon Elastic Container Service (ECS)
  - Amazon Elastic Kubernetes Service (EKS)
  - Azure Kubernetes Services (AKS)
  - Azure Container Instances
  - Azure Container Apps
  - Google Kubernetes Engine (GKE)
  - DigitalOcean DKE
  - Rackspace KaaS

## 03. Understanding kubernetes key concepts

### Kubernetes Architecture

### The Kubernetes APIs

- The API provides a comprehensive view of what you can do with the system as a user.

```
/api/v1
/api/v2
/api/v2alpha3
```

- Kubernetes exposes several sets of REST APIs for different purposes and audiences via API groups.
- Kubernetes defines the following resource categories:
  - **Workloads**: Objects you use to manage and run containers on the cluster.
  - **Discovery and load balancing**: Objects you use to expose your
    workloads to - the world as externally accessible, load-balanced services.
  - **Config and storage**: Objects you use to initialize and configure your applications, and to persist data that is outside the container.
  - **Cluster**: Objects that define how the cluster itself is configured; these - are typically used only by cluster operators.
  - **Metadata**: Objects you use to configure the behavior of other resources - within the cluster, such as HorizontalPodAutoscaler for scaling workloads.

### Distributed system design patterns

## 04. Kubernetes container runtimes

- Kubernetes originally only supported Docker as a container runtime engine. However, that is no longer the case.
- Kubernetes now supports any runtime that implements the CRI interface.
  - **Docker**
  - **containerd**
  - **CRI-O**

### Understanding `Container Runtime Interface (CRI)`

## 05. Kubernetes Distributions

Some of the most popular Kubernetes distributions are as follows:

- minikube (for learning)
- kubeadm
- k3s (IoT & Edge computing)
- k3d (Rancher Lab's minimal distro)
- Rancher Kubernetes (Open source)
- Docker Kubernetes Service (Open source)
- Amazon Elastic Kubernetes Service (Cloud managed)
- Azure Kubernetes Service (Cloud managed)
- Google Kubernetes Engine (Cloud managed)
- RedHat Openshift (Enterprise)
- Tanzu Kubernetes Grid (Enterprise)
- Mirantis (Enterprise)
- Elastisys Compliant Kubernetes (Enterprise)

### Components of Kubernetes distribution

- Container Runtime
- Storage
- Networking

## References

- [Kubernetes Cotnainer Runtimes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)

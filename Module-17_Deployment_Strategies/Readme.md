# Kubernetes Deployment Strategies

- One of the most complicated and risky tasks involved in running a Kubernetes cluster is to upgrade the live application.
- The interactions between different parts of the system when some parts have different versions are often difficult to predict, but in many situations they are required.
- Large clusters with many users cannot afford to be offline for maintenance.
- Microservice architecture helps a lot here. You never upgrade your entire system. You just constantly
  upgrade several sets of related microservices, and if APIs have changed, then you upgrade their clients,
  too.

## 01. What is a Kubernetes Deployment Strategy?

## 02. Different types of K8s deployment strategies

### 2.1 Recreate

### 2.2 Rolling updates

### 2.3 Blue/Green Deployment

### 2.4 Canary Deployment

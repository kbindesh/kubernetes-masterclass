# Securing Kubernetes

In this chapter, we're going to cover the following main topics:

- Securing Kubernetes in layers
- Kubernetes authentication and authorization
- Kubernetes RBAC
- Managing the security of Kubernetes applications

## 00. Understanding K8s Security Challenges

## 01. Securing Kubernetes in layers

- Kubernetes security comprises of

  - Securing **nodes**
  - Securing **networks**
  - Securing **K8s objects** such as Pods

- The Cloud Native Computing Foundation (CNCF) defines Kubernetes security in layers, also known as **4 Cs**:

  1. **Cloud**

  2. **Cluster**
  3. **Container**
  4. **Code**

## 02. Kubernetes Authentication and Authorization

- In the typical K8s workflow, when a request comes through the Kubernetes API server, it invokes an API call.
- This API request needs to be authenticated and authorized by the API server before a request is made to a Kubernetes API resource.
- As a result, the request can either be allowed or denied.

- You can refer to the following article to get an overview of how the Kubernetes authentication process works: https://kubernetes.io/docs/reference/access-authn-authz/authentication/

### 2.1 `Service Accounts` vs `User Accounts`

In Kubernetes, there are two types of accounts used for the authentication purpose, namely:

1. **User Accounts**

   - User account are for normal physical users.
   - In Kubernetes, the RBAC subsystem is used to determine whether the user is authorized to perform a specific operation on a specific scope.

2. **Service Accounts**
   - _Service Accounts_ are for services or processes running in a Pod in the Kubernetes cluster.
   - The service accounts are users managed by the Kubernetes API.
   - In Kubernetes, it is possible to use client certificates, bearer tokens, or even an authenticating proxy to authenticate API requests through an API server.

### 2.2 Kubernetes Service Accounts

    -  sdfg

## 03. Kubernetes RBAC

## 04. Managing the security of Kubernetes applications

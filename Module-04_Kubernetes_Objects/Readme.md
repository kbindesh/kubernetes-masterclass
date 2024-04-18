# Kubernetes Objects

- The fundamental building blocks of any containerized workload that is up and running in the Kubernetes cluster are called **Kubernetes API primitives** or **Kubernetes objects**.
- They are the API resource types defined in Kubernetes, including:
  - **pods**
  - **ReplicaSets**
  - **DaemonSets**
  - **StatefulSets**
  - **Job**
  - **CronJob objects**
  - **Deployments**
  - **Namespaces**
  - **Service**
  - **ConfigMaps**
  - **Volumes**

## `Imperative` vs `Declarative` approaches for K8s object management

- There are a few ways to communicate with API servers in Kubernetes.
  - **Imperative management**
    - Imperative commands
    - Imperative object configuration
  - **Declarative management**

### 01. Imperative Commands

- When using imperative commands, a user operates directly on live objects in a cluster.
- The user provides operations to the kubectl command as arguments or flags.
- This is the recommended way to get started or to run a one-off task in a cluster.
- Because this technique operates directly on live objects, it provides no history of previous configurations.

```
# Run an instance of nginx container by creating a Deployment object
kubectl create deployment nginx --image nginx

```

### 02. Imperative object configuration

- In imperative object configuration, the `kubectl` command specifies the operation (create, replace, etc.), optional flags and at least one file name.
- The file specified must contain a full definition of the object in YAML or JSON format.

```
# Create the objects defined in a .yaml configuration file
kubectl create -f nginx.yaml

# Delete the objects defined in two configuration files
kubectl delete -f nginx.yaml -f redis.yaml

# Update the objects defined in a configuration file
kubectl replace -f nginx.yaml

```

### 03. Declarative object configuration

- In declarative configuration, you define the object definition in YAML config files called a **K8s manifest**.
- When K8s manifests are _applied_ to a kubernetes cluster, kubernetes creates an object based on the configuration.
- When using declarative object configuration, a user **operates on object configuration files** stored locally, however the user does not define the operations to be taken on the files.
- Create, update, and delete operations are automatically detected per-object by `kubectl`.
- This enables working on directories, where different operations might be needed for different objects.
- **Example**: Process all object configuration files in the _configs_ directory

```
# diff to see what changes are going to be made, and then apply

kubectl diff -f configs/

kubectl apply -f configs/

```

- Recursively process directories

```
kubectl diff -R -f configs/

kubectl apply -R -f configs/
```

## What are `Kubernetes Manifest Files`?

- **Kubernetes manifest** files are YAML or JSON files that describe objects in your cluster.
- They're the primary way to manage your objects as they let you version configurations alongside your code, then declaratively apply them to your cluster.
- For example, the following basic manifest (pod.yml) describes a **Pod** called nginx that runs a container using the **nginx:latest** image:

```
apiVersion: apps/v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
```

- You can use `kubectl` to conviniently create the Pod from its manifest in any Kubernetes cluster, without having to remember the syntax of imperative commands like _kubectl run_.

```
# Using an imperative command
$ kubectl run nginx --image nginx:latest

# Declaratively applying a manifest file
$ kubectl apply -f pod.yaml
```

- Manifests are the files that describe what k8s object will be deployed in a Kubernetes cluster.
- Your actual deployments are created by applying your manifest files to your cluster, typically using **kubectl** or a package manager like **Helm**.

### Why use kubernetes manifests?

- Declarative configuration
- Predictable outcomes
- Versioning k8s objects
- Collaborating on K8s objects
- Facilitate automations (CI/CD Pipelines)

### Common Kubernetes manifest fields

- All the kubernetes manifests have a few required fields and some object specific fields.
  - **apiVersion**
  - **kind**
  - **metadata**
  - **spec**

## References

- [Kubernetes API](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.29/)
- [Imperative commands](https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/#imperative-commands)
- [Imperative object configuration](https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/#imperative-object-configuration)

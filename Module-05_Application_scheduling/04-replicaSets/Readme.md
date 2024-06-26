# Understanding ReplicaSets

- **ReplicaSets** help pods achieve higher availability since users can define a certain number of replicas using a ReplicaSet.
- The main capability of a **ReplicaSet** is to make sure the cluster keeps the exact number of replicas running in the Kubernetes cluster.
- If any of them were to fail, new ones would be deployed.

## Step-01: Create a ReplicaSet

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: melonapp-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: melonapp-rs
  template:
    metadata:
      labels:
        app: melonapp-rs
    spec:
      containers:
      - name: nginx
        image: nginx
```

## Step-02: `Get the list` of ReplicaSets

```
kubectl get replicaset

OR

kubectl get rs

# You will see the output indicating the number of DESIRED replica counts and the ones are in a READY state.
```

## Step-03: `Scale the application` by revising the replica count

- Once the ReplicaSet is deployed, update the number of ReplicaSets:

```
# Syntax
kubectl scale replicaset <replicaset_name> --replicas=<revised_count>

kubectl scale replicaset frontend --replicas=5

# You will get the response as "replicaset.apps/frontend scaled"
```

## Step-04: `Delete a ReplicaSet`

```
# Syntax
kubectl delete replicaset <replicaset_name>

# Example
kubectl delete replicaset frontend

```

## Horizontal Pod Autoscaler (HPA)

- HPA is a Kubernetes API primitive that scales the workloads automatically based on application's demands.
- HPA can be used to update a workload resource such as a Deployment or a StatefulSet.

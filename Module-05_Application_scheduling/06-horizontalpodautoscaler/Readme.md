# Scaling Application with HPA

- In order to cope-up with increasingly on-demand requests, we need might need to spin up multiple instances of applications (Pods) to satisfy the workload requirements.
- When you deploy an application using K8s Deployment, scaling is achieved by changing the number of replicas.

## 01. Scale the Deployment manually (imperative)

```
# Syntax
kubectl scale deployment <deployment_name> --replicas=<count>

# Example
kubectl scale deployment webapp-deployment --replicas=5
```

## 02. Scale the ReplicaSet manually (imperative)

```
# Syntax
kubectl scale replicaset <replicaset_name> --replicas=<count>

# Example
kubectl scale replicaset frontend --replicas=6
```

## 03. Horizontal Pod Autoscaler (HPA)

- To update a workload resource such as a **Deployment**, you can use HPA.
- **HPA** is a Kubernetes API primitive that scales the workloads automatically based on enduser's demand.
- **HPA** is configured to fetch metrics provided by a metrics server based on the CPU and memory usage.
- HPA scales the _Deployment_ by increasing or decreasing the count of replicas, which is managed by an underlying ReplicaSet.

## Step-3.1: Create HPA for a Deployment (imperative)

- To create an HPA, you can use the following command:

  **kubectl autoscale deployment**

- Below are the key flags for the above command:

  - **cpu-percent** indicates the average CPU utilization usage across all pods
  - **min** provides the minimum number of replicas
  - **max** provides the maximum number of replicas

- Example

  ```
  kubectl autoscale deployment webapp-deployment --cpu-percent=50 --min=3 --max=10
  ```

## Step-3.2 Get the list of all the HPA

```
kubectl get hpa
```

## Step-3.3: Create HPA for a Deployment (Declarative)

- You can also develop kubernetes manifest for HPA, which will help you in achieving the same objective.

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: bin-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: bin-hpa
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

## Step-3.4: Delete HPA

```
# Syntax
kubectl delete hpa <hpa_name>

# Example
kubectl delete hpa bin-hpa
```

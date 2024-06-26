# Understanding `DaemonSets`

- **DaemonSets** create a multiple copies of a pod, making sure that at least one copy of the pod is present on each node of your Kubernetes cluster.
- If a new node is added to the cluster, a replica of that pod is automatically assigned to that node.
- Likewise, when a node is removed, the pod is automatically removed.
- **Alias**: ds

### Step-01: Create a DaemonSet

- You can define the DaemonSet using the following YAML manifest:

  ```
  apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    name: fluentd
    namespace: kube-system
    labels:
      k8s-app: fluentd
  spec:
    selector:
      matchLabels:
        name: fluentd
    template:
      metadata:
        labels:
          name: fluentd
      spec:
        containers:
          - name: fluentd
            image: fluentd:latest
  ```

### Step-02: List DaemonSets

- You can list all the daemonSets present in a default namespace:

  ```
  kubectl get daemonsets
  ```

- List all the DaemonSets present in a particular namespace:

  ```
  kubectl get daemonsets -n <namespace>

  OR

  kubectl get ds -n <namespace>
  ```

### Step-03: Describe DaemonSets

- You can describe or get more details about a DaemonSet by running following command:

  ```
  # Syntax
  kubectl describe daemonsets <daemonset_name> -n <namespace>

  # Example
  kubectl describe daemonsets fluentd -n kube-system
  ```

### Step-04: Delete a DaemonSet

- In case you want to delete a DaemonSet, use the following command:

  ```
  # Syntax
  kubectl delete ds fluentd -n kube-system

  # Example
  kubectl delete ds fluentd -n kube-system
  ```

# Troubleshooting Cluster components and Applications

- Troubleshooting Kubernetes clusters is a combination of identifying, diagnosing, and remediating an issue – the problem statement covers:

  1. Kubernetes cluster components
  2. Nodes
  3. Networking
  4. Security

- Moreover, the problem statement also covers the application level, such as
  1. pod
  2. container

## 01. Recommeded practices in Kubernetes Troubleshooting

- It is important to take an outside-in approach and gradually narrow down the scope to identify the root cause of an issue.

- This means we can rationalize the process using the following recommendations:

  - **Monitoring**: Monitoring plays a vital role in identifying potential problems and finding their root causes.

  - **Metrics Analysis**: Metrics analysis is the first step shortly after you detect a potential issue. you can make the troubleshooting easier by starting with analyzing metrics from the cluster and node level to get a high-level view, then moving down to the application.
  - **Analyzing the logs**: If you find that you have a better idea about the issue that occurred, it's about time to dive deep into those logs and find the root cause, as compared to the one you thought was the culprit.
  - **Remediating the Issue**: Once you have found the issue, an actionable remediation plan is required if you want to prevent the issue from ever happening again, rather than just applying a quick fix to the issue.

## 02. Troubleshooting cluster components

### 2.1: Inspecting K8s Cluster

```
# Get K8s cluster info
kubectl cluster-info

# To get further info about cluster for debugging and diagnosis
kubectl cluster-info dump
```

- By running above commands, we could get good amount of information around control plane and cluster logs.
- You'll get errors for the workloads running on top of it quite often, which can happen because of the node availability or capability.

### 2.2: Inspecting K8s Worker Nodes

- Inspecting the node will help you get the current state of your kubernetes cluster and worker nodes:

```
# Get the list of worker nodes
kubectl get nodes

[From the preceding command output, check the STATUS column value of particular node]
```

- The **ROLES column** shows the role of your node – it could be a **control-plane**, **etcd**, or **worker**:

  1. **The control-plane** role runs the Kubernetes master components.
  2. **The etcd role** runs the etcd store.

  3. **The worker role** runs the Kubernetes worker node – that is where your containerized workload runs.

- The **STATUS column** shows the current condition of the running nodes – the ideal status that we all
  love is Ready.

| Node Status          | Description                                                                           |
| :------------------- | :------------------------------------------------------------------------------------ |
| _Ready_              | Ready The node is healthy and ready to accept pods.                                   |
| _DiskPressure_       | The disk capacity is low.                                                             |
| _MemoryPressure_     | The node memory is low.                                                               |
| _PIDPressure_        | Too many processes are running on the node.                                           |
| _NetworkUnavailable_ | The networking is incorrectly configured.                                             |
| _SchedulingDisabled_ | This is not a condition in the Kubernetes API but it appears after you cordon a node. |

- In case you have suspicions about the node, you can use the following command to inspect the node information:

```
kubectl describe node <NODE_NAME>

[You will get more info than "kubectl get node" command]

# To envision the node details in a more structured way | Render it in YAML
kubectl get node <NODE_NAME> -o yaml
```

- In case you do not want the details of kubernetes nodes and just want to get the resource untilization (cpu & memory) of the current running process in your Kubernetes cluster:

```
top
```

- As we discussed earlier in this module, _DiskPressure_ is also a key factor in the health status of the worker node. To check the available disk consumption:

```
df -h
```

### 2.3: Troubleshooting Master node App components

```
# Check for errors in a system-reserved process | Pods
kubectl get pods -n kube-system

# To see which system-reserved pods are running on which node | -o wide
kubectl get pods -n kube-system -o wide
```

- When you see any process that is not in the Running status, it means that it is unhealthy. You may describe the pod to get more info:

```
kubectl describe pod <POD_NAME> -n kube-system
```

- In the case that you have multiple nodes and want to see which pod is on which node, you can also use the following command to check out your kube-proxy DaemonSet:

```
kubectl describe daemonset kube-proxy -n kube-system
```

- Knowing the pod configuration is sometime not enough. Especially when the pod is not running due to some reason, the logs are much handier, especially when the Events section is _none_.

```
kubectl logs <KUBE_PROXY_POD_NAME> -n kube-system
```

### 2.4: Troubleshooting Worker node App components

**Troubleshooting the kubelet agent**

- After checking on the node status, we could SSH to that worker node, and use the following command to check on the kubelet status:

```
systemctl status kubelet
```

- In the case that the status is not active (running), we could use journalctl to obtain the logs on the kubelet service on the worker node:

```
journalctl -u kubelet.service

[Then, it is up to you to find out what the main issue from the captured logs]
```

- Note that sometimes there isn't any real issue. After you have checked on the lost logs using the journalctl -u kubelet.service command, you could restart the kubelet agent to fix the issue:

```
systemctl restart kubelet
```

## 03. Troubleshooting Applications

- In this section, we will focus on troubleshooting containerized applications deployed on the Kubernetes cluster.
- This primarily covers issues with containerized-application-related Kubernetes objects, including:

  - **Pods**

  - **Containers**
  - **Services**
  - **StatefulSets**

### 3.1 Getting the high-level view of App failures

### 3.2 Inspecting the Namespace Events

### 3.3 Troubleshooting the failing Pods

### 3.4 Troubleshooting the init ontainers

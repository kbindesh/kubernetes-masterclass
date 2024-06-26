# Managing Storage in Kubernetes

## 01. Understanding Application's Storage Requirements

## 02. Kubernetes Volumes

- There are basically two types of Kubernetes Volumes:
  1. Ephemeral Storage
  2. Persistent Volumes

### 2.1 Ephemeral Storage

- Ephemeral volumes meant for application to hold the data, but they don't really care about data
  loss in the case that the pod fails or restarts
- The lifecycle of the ephemeral volume is aligned with the pod lifecycle.
- Mounted storage is usually ephemeral, as it shares the same lifecycle as your containers.
- As soon as the container is stopped or destroyed during the process of restarting the pod, any internal storage is completely removed.
- There are multiple types of ephemeral storage:

#### 2.1.1 emptyDir (for intra-pod communication)

- The following is an example YAML definition of an emptyDir mounted to a pod:

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: emptydir-vol-pod
  spec:
    restartPolicy: Never
    volumes:
    - name: shared-data
      emptyDir: {}
    containers:
    - name: busybox-container
      image: busybox
      command: ["/bin/sh","-c","while true; do sleep 3600; done"]
      volumeMounts:
      - name: shared-data
        mountPath: /tmp/data
    - name: nginx-container
      image: nginx
      volumeMounts:
      - name: shared-data
        mountPath: /data
  ```

- To use the shared memory option, we just need to add **medium: Memory** to the **emptyDir** section, as follows:

  ```
  volumes:
  - name: shared-data
    emptyDir:
      medium: Memory
  ```

- Note that memory-based emptyDir counts toward the container's memory limit.
- To verify if it works, let's create a pod with two containers and then write a file using one container and read it using the
  other container:

  ```
  kubectl create -f emptydir-vol-pod.yml


  # To list containers belongs to above Pod
  kubectl get pod emptydir-vol-pod -o json | jq .spec.containers


  # Create a file in /tmp/data directory of busybox-container
  kubectl exec -it emptydir-vol-pod -c busybox-container -- touch /tmp/data/File.txt


  # Now, list the created file in /data directory of the nginx-container
  kubectl exec -it emptydir-vol-pod -c nginx-container -- ls /data

  ```

#### 2.1.2 Generic ephemeral volumes

#### 2.1.3 CSI ephemeral volumes

#### 2.1.4 Projected volumes

### 2.2 Persistent Volumes

## 03. Kubernetes Storage Classes

## 04. `Volume modes` and `Access modes` for Volumes

## 05. Configuring an Application with mounted storage

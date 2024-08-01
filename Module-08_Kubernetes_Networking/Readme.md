# Networking in Kubernetes

## 01. Kubernetes Networking Model

- Kubernetes networking resolves the challenge of how to allow different Kubernetes components to communicate with each other.
- It also allows applications on Kubernetes to communicate with other applications, as well as the services outside of the Kubernetes cluster.

Let's understand how k8s networking works in the following use-cases:

- **Container-to-Container communication**

- **Pod-to-Pod communication**

- **Pod-to-Service communication**

- **External-to-Service communication**

- **Node-to-Node communication**

### 1.1 Container-to-container communication

- The following is an example called _multi-container-pod.yaml_ that shows how to create multi-containers in a pod.
- In this pod, it contains **nginx** and **busybox** as two containers where
  busybox is a container that calls nginx container through port _80_ on _localhost_

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: multi-container-pod
    labels:
      app: multi-container
  spec:
    containers:
    - name: nginx
      image: nginx:latest
      ports:
      - containerPort: 80
    - name: busybox-helper
      image: busybox:latest
      command: ['sh', '-c', 'while true; do sleep 600; done;']
  ```

- Deploy the above Pod

  ```
  kubectl apply -f multi-container-pod.yaml
  ```

- Now, let's check whether we can communicate with the nginx container from the busybox helper container:

  ```
  kubectl exec multi-container-pod -c busybox-helper -- wget http://localhost:80

  OR

  kubectl exec multi-container-pod -c busybox-sidecar -- cat index.html
  ```

### 1.2 Pod-to-Pod communication

- In Kubernetes, each pod has been assigned a unique IP address based on the _podCIDR_ range of that worker node.
- This IP assignment is not permanent, as the pod eventually fails or restarts, the new pod will be assigned a new IP address.
- By default, pods can communicate with all pods on all nodes through pod networking without setting up Network Address Translation (NAT).

- The following example demonstrates:

  ```
  # Create an nginx pod
  kubectl run nginx-pod --image=nginx –-port=8080

  # To check the Pod IP address
  kubectl get pod nginx-pod -o wide

  # To check all pods present in default namespace with assigned IP addresses
  kubectl get pods -o wide

  [Notice that the multi-container pod and nginx-pod are in the same podCIDR]

  # Check the podCIDR of a node (here it is minikube)
  kubectl get node minikube -o json | jq .spec.podCIDR

  ```

- **Note**: When we start a vanilla minikube installation with the _minikube start_ command without specifying additional parameters for the CNI network plugin, it sets the default value as _auto_. It chooses a _kindnet plugin_ to use, which creates a bridge and then adds the host and the container to it.

### 1.3 Pod-to-Service communication

- The service accepts traffic from both inside and outside of the cluster.
- The effective communication between Pods and Services involves letting the service expose an application running on a set of pods.

- **Kubernetes Service Types**

  1. **ClusterIP**

     - A default service type for Kubernetes.
     - For internal communications, exposing the service makes it reachable within the cluster.

  2. **NodePort**
     - For both internal and external communication.
     - NodePort exposes the service on a static port on each worker node.
     - Example -
       ```
       # For external communication
       <node_ip_address>:<node_port>
       ```
  3. **LoadBalancer**

     - Works with cloud providers, as it is backed by thier respective load balancer service offerings. Ex. AWS ALB
     - Underneath LoadBalancer, ClusterIP and NodePort are created, which are used for internal and external communication.

  4. **ExternalName**

     - Maps the _service_ to the contents with a _CNAME record_ with its value.
     - It allows external traffic access through it.

#### 1.3.1 ClusterIP

- Let's go ahead and deploy an application and do a deeper dive.

- **Imperative way**

  To create a deployment called nginx and with 2 replicas, use the following command:

  ```
  # Deploy nginx application in default namespace
  kubectl create deployment nginx --image=nginx --replicas=2

  # Get the details of above deployment
  kubectl get deploy nginx -o wide

  # Get list of all the Pods in default namespace
  kubectl get pods

  # Exposing the pods to the K8 cluster | Ceate a ClusterIP service
  kubectl expose deployment nginx --type=ClusterIP --port 8080 --name=melon-service --target-port 80

  # List all the services in default namespace
  kubectl get svc

  [Observe the ClusterIP service IP and the port details]
  ```

- **Declarative way**

  What we did in this section to create a new ClusterIP service by using the _kubectl expose_ command can also be done using the following YAML manifest file:

  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: bin-cip-service
  spec:
    type: ClusterIP
    selector:
      app: nginx
    ports:
      - protocol: TCP
        port: 8080
        targetPort: 80
  ```

- In the preceding manifest, we can see there's a section called _selector_. This section has a key-value pair, **app:nginx**, that has a _label sector_.
- We use a **selector** to map the _service_ with the _pods_.

- Now, let's develop K8s manifest for **nginx deployment**

  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nginx
  spec:
    selector:
      matchLabels:
        app: nginx
    replicas: 2
    template:
      metadata:
        labels:
          app: nginx
        spec:
          containers:
            - name: nginx
              image: nginx
              ports:
              - containerPort: 80
  ```

- In the preceding manifest, we can see that there is a section to specify the selector and we used the same key-value pair, **app: nginx**, to map the ClusterIP specification so that it worked as expected.

- You can use the following commands to get the endpoints of ClusterIP service - **bin-service** :

  ```
  kubectl get endpoint bin-service

  OR

  kubectl get ep bin-service
  ```

- Render the running clusterip service in YAML to get more details:

  ```
  kubectl get svc bin-service -o yaml
  ```

#### 1.3.2 NodePort

- NodePort opens ports on the Kubernetes nodes, which usually are de facto virtual machines.
- NodePort exposes access through the IP of the nodes and, with the opened port, makes the application accessible from outside of the Kubernetes cluster.
- The network traffic is forwarded from the ports to the service.
- _kube-proxy_ allocates a _port in the range 30000 to 32767 on every node_.

- Let's create a deployment called _webapp-deployment_ with a 3 replicas.

- **Imperative way**

  ```
  kubectl create deployment webapp-deployment --image=nginx --replicas=3

  # Expose the above created deployment (webapp-deployment) using NodePort
  kubectl expose deployment webapp-deployment --port=8080 --targetport=80 --type=NodePort

  ```

- **Important Note**

  - If you don't provide a _target port_, it is assumed to be the same as the container port.
  - If you don't provide a _node port_, a free port in the range between **30000** and **32767** is automatically allocated.

- Now, list all the services. As we didn't specify the name in the previous
  command, the service name is presumed to be the same as the application name.

  ```
  # List all the services from default namespace
  kubectl get svc

  # Get details of NodePort service -> webapp-deployment
  kubectl get svc webapp-deployment -o wide
  ```

- From the preceding command output, we can see the port is exposed at xxxxx, which is within the range of **30000 to 32767** on the node, and the target port is 80, which is listening at the container level.

- Now, let's get the worker node IP (Internal IP)

  ```
  kubectl get node -o wide
  ```

- From the preceding output, you will get the internal IP of the node, as we're testing locally, so we can use the _internal IP_ and _port_ in conjunction to connect to webapp-deployment.

  ```
  <node_internal_ip>:<nodePort>
  ```

#### 1.3.3 LoadBalancer

- LoadBalancer is a standard way to connect a service from outside of the cluster.
- In this case, a network load balancer redirects all external traffic to a service and each service gets its own IP address.
- It allows the service to load balance the network traffic across applications
- It only works in a cloud environment or another environment that supports external load balancers.
- Deploying the LoadBalancer service to get a public IP is commonly used in managed Kubernetes distributions such as:

  - **Azure Kubernetes Service (AKS)** | LoadBalancer is the default outbound type for AKS
  - **Elastic Kubernetes Service (EKS)**
  - **Google Kubernetes Engine (GKE)**

- **Imperative way**

  ```
  kubectl expose deployment nginx --port=80 --target-port=8080 --name=bin-lb-svc --type=LoadBalancer
  ```

- **Declarative way**

  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: bin-lb-svc
  spec:
    type: LoadBalancer
    ports:
    - port: 80
      targetPort: 8080
    selector:
      app: bin-web-app
  ```

#### 1.3.4 ExternalName

- ExternalName maps the service to the contents with a [CNAME record](https://support.dnsimple.com/articles/differences-between-a-cname-alias-url/) with its value.

- It allows external traffic to access it.

  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: bin-external-svc
    namespace: prod
  spec:
    type: ExternalName
    externalName: my.binapp.solutions.com
  ```

### 1.4 Node-to-Node communication

- Within a K8s cluster, each node is registered by the _kubelet agent_ to the master node, and each node
  is assigned a node IP address so they can communicate with each other.

  ```
  # To check the internal IP of all the cluster nodes
  kubectl get nodes -o wide
  ```

- In the case that we have multiple nodes, we can ping each node from the node within the same network.
- We need to ensure the connectivity between master nodes and worker nodes, so the workloads get to be scheduled to the worker node.

## 02. Configuring Kubernetes Networking on the Cluster nodes

## 03. Exposing Services with `Ingress`

### 3.1 Introducing Ingresses

- In Kubernetes, an Ingress is a way for external clients to access the services of applications running in the cluster.

- The Ingress function consists of the following three components:

  1. **Ingress API object**, which is used to define and configure an ingress.
  2. **L7 load balancer or reverse proxy**, that routes traffic to the backend services.
  3. **Ingress controller**, which monitors the Kubernetes API for Ingress objects and deploys and configures the load balancer or reverse proxy.

- Unlike a forward proxy, which routes and filters outgoing traffic and is typically located in the same location as the clients it serves, a reverse proxy handles incoming traffic and routes it to one or more backend servers.

### 3.2 Installing an ingress controller

- Before you start creating Ingresses, you need to make sure that an ingress controller runs in your cluster.
- As you learned in the previous section, not all Kubernetes clusters have one.
- If you're using a managed cluster with one of the major cloud providers, an ingress controller is already in place.

  - In **Google Kubernetes Engine**, the ingress controller is GLBC (GCE L7 Load Balancer).

  - In **AWS** the Ingress functionality is provided by the AWS Load Balancer Controller.
  - In **Azure**, it provides AGIC (Application Gateway Ingress Controller).

- Check your cloud provider's documentation to see if an ingress controller is provided or you need to enable it..
- Alternatively, you can install the ingress controller yourself.

- **Nginx ingress controller**

  - There are two implementations of the Nginx ingress controller:

    1. Provided by the Kubernetes maintainers
    2. Provided by the authors of Nginx itself

  - If you're new to Kubernetes, you should start with the first one. That is the one I used.

- You should be able to install the Nginx ingress controller by following the instructions at https://kubernetes.github.io/ingress-nginx/deploy/.

- If you created your cluster using the **kind** tool, you can install the controller by running the following command:

  ```
  $ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress
  ```

- If you run your cluster with Minikube, you can install the controller as follows:

  ```
  $ minikube addons enable ingress
  ```

### 3.3 Creating and using Ingress objects

#### 3.3.1 Exposing a service through an Ingress

- An Ingress object references one or more Service objects.

- Your first Ingress object exposes the bin-app-svc service, which you created in the previous section.
- Before you create the Ingress object, refresh your memory by looking at the service manifest in the following listing:

  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: bin-app-svc
  spec:
    type: ClusterIP
    selector:
      app: bin-app
    ports:
    - name: http
      port: 80
      targetPort: 8080
    - name: https
      port: 443
      targetPort: 8443
  ```

- The Service type is ClusterIP because the service itself doesn't need to be directly accessible to clients outside the cluster, since the Ingress will take care of that.
- Although the service exposes ports 80 and 443, the Ingress will forward traffic only to port 80.

- Now, let's create an Ingress object manifest which will expose the bin-app-svc service

  ```
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: bin-example-com
  spec:
    rules:
    - host: bin.example.com
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: bin-app-svc
              port:
                number: 80
  ```

#### 3.3.2 Path-based ingress traffic routing

## 04. Configuring and leveraging CoreDNS

# Install and Configure `minikube` Kubernetes

- minikube is local Kubernetes (single node), focusing on making it easy to learn and develop for Kubernetes.
- What you'll need:
  - 2 CPUs or more
  - 2GB of free memory
  - 20GB of free disk space
  - Internet connection
  - Container or virtual machine manager, such as: Docker, QEMU, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMware Fusion/Workstation

## `minikube` on EC2 Instance (Amazon Linux)

### Step-01: Create an AWS EC2 instance

- AMI: Amazon Linux 2/Amazon Linux 2023
- Instance Type: <any_two_vcpu_instance_type> e.g t3.small/t3.medium
- Ingress: Allow SSH
- Egress: Allow all
- VPC & Subnet: Default
- Key Pair: <create_a_new_keypair>

### Step-02: Install `Docker Engine`

```
# Install Docker on Amazon linux 2
sudo amazon-linux-extras install -y docker

# Install Docker on Amazon linux 2023
sudo yum install -y docker

# Start the docker service
sudo systemctl start docker

# Enable the docker service
sudo systemctl enable docker

# Add the ec2-user to docker group
sudo gpasswd -a ec2-user docker
OR
sudo usermod -a -G docker ec2-user

# Logout/shutdown and reconnect to your Docker host (IMP)
exit
OR
shutdown -r now

# Now, try to run any docker command without prefixing it with sudo
docker ps

# You should be able to get the results with NO permission related errors
```

- For `Docker engine` installation on other linux distributions, refer this link: https://docs.docker.com/engine/install/
- For `Docker desktop` installation on Windows, MacOS and Linux distributions, refer this link: https://docs.docker.com/desktop/

### Step-03: Install `kubectl` (CLI) utility

- `kubectl` is a utility to send instructions to manage a K8s cluster.
- Hence it is required to install it before you configure or install the K8s cluster

```
 # Download the kubectl binary
 sudo curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

 # Provide execute permission to kubectl
 sudo chmod +x kubectl

 # Move the kubectl program to /usr/local/bin in order to access it from anywhere
 sudo mv kubectl /usr/local/bin

```

### Step-04: Install and Configure `minikube` (kubernetes distro)

- Once both Docker and Kubectl are installed, you may use following set of commands to install `minikube`:

```
# Download the minikube binary
sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

# Install the minikube in /usr/local/bin/ directory
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Restart the VM
shutdown -r now
```

### Step-05: Start the `minikube`

```
 # Start the minikube cluster
 minikube start

 # You will see a set of messages appear on the console.

 # Check the minikube k8s status | should be in running state
 minikube status

# Verifying the minikube cluster installation
kubectl get node
```

- Your minikube cluster contains one node that serves as both the control plane and worker node.
- That means that once you have it set up, you can start to schedule workloads in your local Kubernetes cluster.

### Step-06: Configuring the minikube cluster (OPTIONAL)

- The **minikube config set** command will help you apply the settings such as _CPU_ and _memory_ that you'll allocate to the minikube cluster.

```
minikube stop

minikube config set memory 8192

minikube config set cpus 4

minikube start

```

### Step-07: Deleting a minikube cluster (OPTIONAL)

- The following command deletes all local Kubernetes clusters and all profiles:

```
minikube delete --all
```

## References

- [Amazon Linux 2023 FAQs](https://aws.amazon.com/linux/amazon-linux-2023/faqs/)
- [Minikube Official Documentation](https://minikube.sigs.k8s.io/docs/)
- [Install and setup kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

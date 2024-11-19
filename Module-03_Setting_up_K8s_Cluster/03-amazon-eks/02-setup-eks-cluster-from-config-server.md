# Setup Amazon EKS cluster from Config Server (EC2 Instance) using `eksctl`

- In this section, we will learn how to setup an Amazon EKS cluster, a Production-grade Kubernetes distribution from a `config server` which we'll setup on Amazon EC2 instance.

## Step-01: Setup `Config server` on EC2 Instance

### 1.1: Setup `Config Server` on EC2 Instance

- AWS Management Console >> EC2 >> Launch Instances
- Name: `config-server`
- AMI: Amazon Linux 2
- Instance Type: t2.micro
- Network Settings
  - VPC: Default
  - Subnet: Default
  - Public IP: Enabled
  - Security Group: Allow Ingress traffic on port 22
- Storage settings
  - Root Volume size: 15GB

### 1.2: Install `kubectl` on Config server

- Ref: *https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html*

```
# Download the kubectl binary
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Assign execute permission to the kubectl utility
chmod +x kubectl

# Move the kubectl utility to /usr/local/bin
mv kubectl /usr/local/bin
```

### 1.3: Install `eksctl` on Config server

- *https://eksctl.io/installation/*

```
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

# Download the eksctl binary
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"

# (Optional) Get the eksctl checksum
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

# Extract the downloaded tarball
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

# Move the eksctl utility to /usr/local/bin
sudo mv /tmp/eksctl /usr/local/bin
```

### 1.4: Install `Git` on Config server

```
sudo yum install -y git
```

## Step-02: Create & Configure `IAM Roles & Policies` for Config server (to setup EKS cluster)

- This reference doc link describes the minimum IAM policies needed to run the main use cases of eksctl: </br> *https://eksctl.io/usage/minimum-iam-policies/*

### 2.1: Create an IAM policy - `EksAllAccess`

- AWS Management console >> IAM >> Policies >> Create Policy
- Open JSON Editor (click on JSON button).
- Copy the **EksAllAccess** IAM Policy from [here](./iam-policies/EksAllAccess.json) and paste it in the policy JSON editor.
  - _IMP_: Replace the existing AccountID with your AWS AccountID
- **Policy Name**: EksAllAccess
- Click on **Create Policy** button

### 2.2: Create an IAM policy - `IamLimitedAccess`

- AWS Management console >> IAM >> Policies >> Create Policy
- Open JSON Editor (click on JSON button).
- Copy the **IamLimitedAccess** IAM Policy from [here](./iam-policies/IamLimitedAccess.json) and paste it in the policy JSON editor.
  - _IMP_: Replace the existing AccountID with your AWS AccountID
- **Policy name**: IamLimitedAccess
- Click on **Create Policy** button

### 2.3: Create an AWS IAM Role for Config server - `SetupEKSClusterRole`

- Create an IAM Role for Config server with access to following services:

  - IAM
  - EC2
  - CloudFormation
  - EKS
  - ECR
  - VPC

- AWS Management console >> IAM >> Roles >> Create Role.
- **Trusted entity type**: AWS service
  - Service or Use case: EC2
- **Add Permissions**
  - Select following `IAM policies` from the list:
    - AmazonEC2FullAccess
    - AWSCloudFormationFullAccess
    - EksAllAccess
    - IamLimitedAccess
- **Name, Review & Create**
  - Role Name: `SetupEKSClusterRole`
  - Description: This IAM role is responsible for setting-up EKS cluster from config server.

## Step-03: Assign IAM role to Config server

- AWS Management console >> EC2 >> Select you Config Server (EC2 Instance)
- Click on **Actions** menu >> Security >> Modify IAM Role >> Select `SetupEKSClusterRole` role we created in the preceding step.

## Step-04: Create an Amazon EKS cluster & Node groups

### 4.1 Create EKS cluster using `eksctl`

```
eksctl create cluster --name=labekscluster --region=us-east-1 --zones=us-east-1a,us-east-1b --without-nodegroup

# Get List of clusters
eksctl get cluster
```

:warning: **The cluster creation process can take around 10 to 15 mins to complete.**

### 4.2 Create EC2 Keypair for Nodegroup EC2 instances

- Create a new EC2 Keypair with name as `eks-nodegrp-keypair`

### 4.3 Create Node Group with additional add-ons in public subnets

- Note: Create an EC2 keypair (here, it is binWinWebServerKey) for your EKS nodes manually and pass the same keypair name in the following command:

```
# Create public Node Group
eksctl create nodegroup --cluster=labekscluster --region=us-east-1 --name=eksdemo1-ng-public1 --node-type=t3.small --nodes=2 --nodes-min=2 --nodes-max=4 --node-volume-size=20 --ssh-access --ssh-public-key=binWinWebServerKey --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access
```

## Step-05: Verify EKS Cluster & Worker nodes

### 5.1: Verify NodeGroup subnets | Make sure EC2 instances are in public subnet

- Go to **Services** -> **EKS** -> **eksdemo** -> **eksdemo1-ng1-public**
- Click on **Associated subnet** in Details tab
- Click on **Route Table** Tab.
- We should see that internet route via Internet Gateway (0.0.0.0/0 -> igw-xxxxxxxx)

### 5.2: Verify EKS Cluster and NodeGroup from AWS management console

- Navigate to **Services** -> **Elastic Kubernetes Service** -> **<eks_cluster_name>**

### 5.3: List Worker nodes

```
# List EKS clusters
eksctl get cluster

# List NodeGroups in a cluster
eksctl get nodegroup --cluster=<clusterName>

# List Nodes in current kubernetes cluster
kubectl get nodes -o wide

# Our kubectl context should be automatically changed to new cluster
kubectl config view --minify
```

### Kubernetes Architecture

Kubernetes is an open-source container orchestration system. Its architecture is divided into two primary zones: the **Control Plane** and the **Worker Nodes**. 

                       +-----------------------------------+

                       |           Control Plane           |
                       |  +-----------------------------+  |
                       |  | API Server                  |  |
                       |  | Scheduler                   |  |
                       |  | Controller Manager          |  |
                       |  | etcd                        |  |
                       |  +-----------------------------+  |
                       +-----------------------------------+
                                         |
                                         |
                       +-----------------+-----------------+

                       |                                   |
        +-----------------------------+     +-----------------------------+

        |        Worker Node 1        |     |        Worker Node 2        |
        |  +-----------------------+  |     |  +-----------------------+  |
        |  | kubelet               |  |     |  | kubelet               |  |
        |  | kube-proxy            |  |     |  | kube-proxy            |  |
        |  | Pods                  |  |     |  | Pods                  |  |
        |  +-----------------------+  |     |  +-----------------------+  |
        +-----------------------------+     +-----------------------------+

### 1. Control Plane (The Brain)

The Control Plane manages the worker nodes and the pods in the cluster. It makes global decisions, detects cluster events, and responds to them. 

* **API Server (kube-apiserver)**: The entry point for all administrative tasks. It exposes the Kubernetes API and validates configuration data.
* **Scheduler (kube-scheduler)**: Watches for newly created pods with no assigned node and selects a worker node for them to run on.
* **Controller Manager (kube-controller-manager)**: Runs controller processes that regulate the state of the cluster (e.g., Node Controller, Job Controller).
* **etcd**: A consistent and highly-available key-value store used as Kubernetes' backing store for all cluster data.

### 2. Worker Nodes (The Muscle)

Worker Nodes maintain running pods and provide the actual Kubernetes runtime environment. 

* **kubelet**: An agent that runs on each node in the cluster. It ensures that containers are running in a Pod according to the specifications.
* **kube-proxy**: A network proxy that runs on each node, maintaining network rules to allow network communication to your Pods.
* **Pods**: The smallest deployable units of computing that you can create and manage in Kubernetes. They host your containerized applications.


### 1. Control Plane Components

### API Server (kube-apiserver)

* Entry point for all Kubernetes operations.
* Receives requests from users, kubectl, and other components.
* Validates and processes API requests.

### etcd

* Distributed key-value database.
* Stores all cluster configuration and state information.
* Acts as the single source of truth for the cluster.

### Scheduler (kube-scheduler)

* Decides on which worker node a pod should run.
* Considers CPU, memory, affinities, taints, and constraints.

### Controller Manager (kube-controller-manager)

Runs various controllers such as: 

* Deployment Controller
* ReplicaSet Controller
* Node Controller
* Job Controller
* Namespace Controller

Ensures the actual state matches the desired state. 

### 2. Worker Node Components

### kubelet

* Agent running on each node.
* Communicates with the API Server.
* Ensures containers in pods are running as expected.

### kube-proxy

* Handles network communication.
* Maintains Service networking and load balancing rules.

### Container Runtime

Responsible for running containers. 

**Examples:** 

* containerd
* CRI-O

### 3. Pods

Smallest deployable unit in Kubernetes. 
One or more containers share: 

* Network namespace
* Storage volumes

**Example:** 
```
yaml

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
```
Use code with caution.

### Request Flow Example

When you run: 
```
bash

kubectl apply -f deployment.yaml
```
Use code with caution.

1. Request goes to API Server.
2. Configuration is stored in etcd.
3. Scheduler selects a worker node.
4. kubelet creates the pod on the selected node.
5. kube-proxy enables network access.
6. Application becomes available.

### Interview Answer

Kubernetes architecture consists of a Control Plane and Worker Nodes. The Control Plane contains API Server, Scheduler, Controller Manager, and etcd, which manage the cluster. Worker Nodes contain kubelet, kube-proxy, container runtime, and Pods. The API Server acts as the entry point, etcd stores cluster state, Scheduler places pods on nodes, and Controllers maintain the desired state of the cluster.

## Kubernetes CNI (Container Network Interface)

K8s CNI (Container Network Interface) is the networking standard used by Kubernetes to connect pods to the network.
### What CNI does

#### When Kubernetes creates a pod, the CNI plugin:

1. Assigns an IP address to the pod.
2. Connects the pod to the cluster network.
3. Configures routing between pods.
4. Applies network policies (if supported).

How it works
```
Kubernetes 
   | 
kubelet
   |
CNI Plugin
   | 
Network Configuration
```
The kubelet calls the configured CNI plugin whenever a pod is created or deleted.

markdown

## Popular Kubernetes CNI Plugins

| CNI Plugin | Key Features |
| :--- | :--- |
| **Calico** | Networking + Network Policies, BGP support |
| **Cilium** | eBPF-based, high performance, observability |
| **Flannel** | Simple overlay networking |
| **Weave Net** | Easy setup, mesh networking |
| **Antrea** | Open vSwitch based |
| **AWS VPC CNI** | Native AWS VPC networking for EKS |
| **Azure CNI** | Native Azure networking for AKS |

##### Example

To check the CNI running in your cluster:
```
kubectl get pods -n kube-system
```
Look for pods such as:
```
calico-node
cilium
kube-flannel-ds
aws-node
azure-cni
```
View CNI configuration
```
ls /etc/cni/net.d/
cat /etc/cni/net.d/*.conf
```
#### Why CNI is important
1. Enables Pod-to-Pod communication
2. Enables Pod-to-Service communication
3. Supports Network Policies
4. Provides IP address management (IPAM)
5. Integrates Kubernetes with cloud and on-premises networks

In short, CNI is the networking layer that makes Kubernetes pods communicate with each other and the outside world.


## In Docker, CMD and ENTRYPOINT define what runs when a container starts.
### CMD

CMD provides the default command or arguments for a container.

#### Example

```
FROM nginx

CMD ["nginx", "-g", "daemon off;"]
```
When you run:
```
docker run myimage
```

Docker executes:

```
nginx -g "daemon off;"
```

Override CMD:
```
docker run myimage echo hello
```
Output:
```
hello
```
The new command replaces the CMD instruction.

### ENTRYPOINT

ENTRYPOINT specifies the main executable that always runs.

#### Example
```
FROM ubuntu

ENTRYPOINT ["echo"]
```
Run:
```
docker run myimage hello
```
Result:
```
hello
```
Here, hello is passed as an argument to the ENTRYPOINT command.

### CMD + ENTRYPOINT Together

This is the most common pattern.

```
FROM ubuntu

ENTRYPOINT ["echo"]
CMD ["Hello World"]
```
Run:

```
docker run myimage
```
Output:

```
Hello World
```
Override CMD:

```
docker run myimage Kubernetes
```

Output:

```
Kubernetes
```
Docker executes:

```
echo Kubernetes
```

### Difference: CMD vs ENTRYPOINT

| CMD | ENTRYPOINT |
| :--- | :--- |
| Provides default command/arguments | Defines the main executable |
| Easily overridden at runtime | Not overridden unless `--entrypoint` is used |
| Can be omitted | Usually used when container has a fixed purpose |


### Override ENTRYPOINT

Docker allows overriding ENTRYPOINT:

```
docker run --entrypoint /bin/bash myimage
```

This starts Bash instead of the image's configured ENTRYPOINT.


## Interview Answer

ENTRYPOINT specifies the main process that always runs when a container starts, while CMD provides default arguments or a default command. When both are used together, ENTRYPOINT defines the executable and CMD supplies its default parameters, which can be overridden during docker run.

## Terraform Import

terraform import is used to bring existing infrastructure resources under Terraform management without recreating them.
Syntax
```
terraform import [options] ADDRESS ID
```
ADDRESS: Terraform resource address in your configuration.
ID: Existing resource identifier from the provider.
#### Example: Import an AWS EC2 Instance
Create the resource block in Terraform:
```
resource "aws_instance" "web" {
  # configuration will be updated later
}
```
Import the existing EC2 instance:
```
terraform import aws_instance.web i-0123456789abcdef0
```
Verify:
```
terraform state list
terraform state show aws_instance.web
```
Example: Import an Azure Resource Group
```
resource "azurerm_resource_group" "rg" {
}
```
```
terraform import azurerm_resource_group.rg \
/subscriptions/<subscription-id>/resourceGroups/my-rg
```
Example: Import a Kubernetes Namespace
```
resource "kubernetes_namespace" "dev" {
  metadata {
    name = "dev"
  }
}
```
```
terraform import kubernetes_namespace.dev dev
```

#### Important Notes
Import only adds the resource to the Terraform state.
Terraform does not automatically generate complete .tf configuration for imported resources.
After import, update your Terraform code to match the actual resource configuration.
Run:
```
terraform plan
```
to identify differences between your configuration and the imported resource.

Useful Commands
```
# List imported resources
terraform state list

# Show resource details from state
terraform state show <resource>

# Remove from state only
terraform state rm <resource>

# Move resource in state
terraform state mv <source> <destination>
```
##### Terraform 1.5+ Import Block

Instead of the CLI command, you can define imports in code:
```
import {
  to = aws_instance.web
  id = "i-0123456789abcdef0"
}
```
Then run:
```
terraform plan
terraform apply
```
This approach is preferred for Infrastructure as Code because the import operation can be tracked and reviewed alongside your Terraform configuration.
### What is a Data Source in Terraform? 

A data source allows Terraform to read existing information from a cloud provider, service, or infrastructure without creating or modifying it.

##### Example

Read an existing AWS VPC:
```
data "aws_vpc" "existing" {
  id = "vpc-12345678"
}

output "vpc_cidr" {
  value = data.aws_vpc.existing.cidr_block
}
```
Here:

data = Data source block
aws_vpc = Data source type
existing = Local name
Terraform fetches the VPC details from AWS

##### Data Source vs Resource

Resource creates or manages infrastructure:
```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```
Data Source only reads existing infrastructure:
```
data "aws_ami" "latest" {
  most_recent = true

  owners = ["amazon"]
}
```
##### Common Data Sources
Get Latest AMI
```
data "aws_ami" "amazon_linux" {
  most_recent = true

  owners = ["amazon"]
}
```
Get Existing Security Group
```
data "aws_security_group" "sg" {
  name = "web-sg"
}
```
Get Current AWS Account ID
```
data "aws_caller_identity" "current" {}

output "account_id" {
  value = data.aws_caller_identity.current.account_id
}
```
## Import vs Data Source

| Feature | Terraform Import | Terraform Data Source |
| :--- | :--- | :--- |
| **Purpose** | Manage existing resource | Read existing resource |
| **Creates resource** | No | No |
| **Modifies resource** | Yes (after import) | No |
| **Stored in Terraform state** | Yes | Read-only state information |
| **Configuration block** | `resource` | `data` |
| **Lifecycle managed by Terraform** | Yes | No |

###### Interview Answer

Terraform Data Sources are used to fetch information about existing infrastructure or external resources. Unlike resources, data sources do not create or modify anything; they only read and provide data that can be used by Terraform configurations. Examples include fetching existing VPCs, AMIs, Security Groups, and account information.

##### Interview Answer (Short)

Terraform Import is used to bring existing infrastructure under Terraform management by adding it to the Terraform state.
 Terraform Data Source is used to read information about existing resources without managing or modifying them. Import is for ownership and lifecycle management, while a data source is for reference and consumption of existing infrastructure.

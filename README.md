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

 ## GitHub Actions Workflow

A GitHub Actions workflow is an automated process defined in a YAML file that runs when specific events occur in a GitHub repository.

The workflow files are stored in:
```
.github/workflows/
```
Workflow Structure
```
name: CI Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Run Command
        run: echo "Hello GitHub Actions"
```
#### Key Components
1. name

Workflow name displayed in GitHub.
```
name: CI Pipeline`
```
2. on

Defines the trigger event.
```
on:
  push:
  pull_request:
```
Common triggers:
```
on:
  push:
  pull_request:
  workflow_dispatch:
  schedule:
```
3. jobs

A workflow contains one or more jobs.
```
jobs:
  build:
```
4. runs-on

Specifies the runner.
```  runs-on: ubuntu-latest ```
Options:
```
runs-on: ubuntu-latest
runs-on: windows-latest
runs-on: macos-latest
runs-on: self-hosted
```
5. steps

Tasks executed within a job.
```
steps:
  - name: Checkout
    uses: actions/checkout@v4

  - name: Run Tests
    run: npm test
```
CI/CD Example
Build and Test Java Application
```
name: Java CI

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Build
        run: mvn clean install

      - name: Test
        run: mvn test
```
Docker Build Example
```
name: Docker Build

on:
  push:
    branches:
      - main

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Build Image
        run: docker build -t myapp:latest .
```
Kubernetes Deployment Example
```
name: Deploy to K8s

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Configure kubectl
        run: |
          mkdir -p ~/.kube
          echo "${{ secrets.KUBE_CONFIG }}" > ~/.kube/config

      - name: Deploy
        run: kubectl apply -f deployment.yaml
```
Frequently Used Actions
```
actions/checkout
actions/setup-java
actions/setup-node
docker/login-action
docker/build-push-action
azure/login
aws-actions/configure-aws-credentials
```
### Interview Answer

GitHub Actions is GitHub's CI/CD platform used to automate software workflows. A workflow is defined in a YAML file under .github/workflows/ and consists of triggers (on), jobs, runners (runs-on), and steps. It can be used to build, test, scan, package, and deploy applications automatically whenever code is pushed, a pull request is created, or a manual trigger occurs.

## Kubernetes Full Pod Creation Workflow (End-to-End Example)

Let's take a simple example:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
```
Deploy:
```
 kubectl apply -f nginx-pod.yaml
```
#### Complete Workflow
```
User
  |
  | kubectl apply
  v
API Server
  |
  v
etcd
  |
  v
Scheduler
  |
  v
Worker Node
  |
  +--> kubelet
         |
         +--> Container Runtime (containerd)
         |
         +--> CNI (Calico/Cilium)
         |
         v
       Running Pod
```
Step 1: User Sends Request
```
kubectl apply -f nginx-pod.yaml
```
kubectl sends the Pod manifest to the API Server.

Step 2: API Server

Responsibilities:

Authenticates user
Validates YAML
Checks RBAC
Accepts request

Example:
``` kubectl --> API Server ```
After validation, the Pod object is created.
Step 3: Store in etcd

The API Server stores the desired state in:
``` etcd```
Example entry:
```
Pod Name : nginx-pod
Status   : Pending
Node     : None
```
At this stage:
```
kubectl get pods
```
```
nginx-pod   Pending
```
Step 4: Scheduler Selects Node

Scheduler continuously watches for Pods with:
```
nodeName = null
```
Checks:

CPU availability
Memory availability
Affinity rules
Taints/Tolerations

Suppose Node-2 is selected:
```
nginx-pod --> worker-node-2
```
Scheduler updates the Pod object.
Step 5: kubelet Receives Assignment

On worker-node-2:
``` kubelet ```
detects:
A new pod is assigned to me

kubelet starts Pod creation.

Step 6: Container Runtime Pulls Image

kubelet contacts containerd:
``` kubelet ---> containerd``` 
containerd executes:
```
docker pull nginx:latest
```
(Internally containerd pulls from Docker Hub.)

If image already exists:

Uses local cache

Step 7: Create Pod Sandbox

Container Runtime creates:
Pause Container

Example:
```
nginx-pod
 |
 +-- pause container
 +-- nginx container
```
Purpose:

Holds Pod network namespace
Holds Pod IP

Verify:
```
crictl ps
```
Step 8: CNI Plugin Executes

Now kubelet calls the CNI plugin.

Examples:

Calico
Cilium
Flannel

Tasks:

Assign IP

Example:
``` 10.244.1.25``` 
Configure Routes

Pod A ---> Pod B
Create Interfaces

Example:
eth0
inside pod.

Step 9: Start Application Container

containerd creates:

```
nginx container
```
using image:
``` nginx:latest ```
Process starts:

nginx master process
Step 10: Health Checks

If configured:
```
livenessProbe:
readinessProbe:
startupProbe:
```
kubelet runs them.

Example:
```
readinessProbe:
  httpGet:
    path: /
    port: 80
```
Result:
Ready = True

Step 11: Pod Running

Status changes:
```
Pending
   |
ContainerCreating
   |
Running
```
kubectl get pods

```
NAME        READY   STATUS
nginx-pod   1/1     Running
```
## Service Access Workflow

Create Service:
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
```
Workflow:
```
Client
  |
  v
Service IP
  |
  v
kube-proxy
  |
  v
Pod IP
  |
  v
nginx Pod
```
kube-proxy updates iptables/IPVS rules and forwards traffic.
### What Happens When Pod Is Deleted?
```
kubectl delete pod nginx-pod
```
Flow:
```
API Server
     |
     v
kubelet
     |
containerd stops container
     |
CNI releases IP
     |
Pod removed
```
## Components Involved in Interview Order
Control Plane
API Server
etcd
Scheduler
Controller Manager
Worker Node
kubelet
Container Runtime (containerd/CRI-O)
CNI Plugin (Calico/Cilium/Flannel)
kube-proxy
Pod Containers

#### 2-Minute Interview Answer

When a Pod is created, kubectl sends the manifest to the API Server. The API Server validates the request and stores it in etcd. The Scheduler selects a suitable worker node and assigns the Pod. The kubelet on that node receives the assignment and instructs the container runtime (containerd/CRI-O) to pull the image and create the Pod sandbox. The CNI plugin configures networking and assigns a Pod IP. The application container starts, health checks are performed, and the Pod reaches the Running state. If a Service is present, kube-proxy routes traffic to the Pod.

## Main Types of Kubernetes Controllers
1. Replication Controller (Legacy)

Ensures a specified number of pod replicas are running.

YAML
```
apiVersion: v1
kind: ReplicationController
```
Show more lines

Mostly replaced by ReplicaSets and Deployments.

2. ReplicaSet Controller

Maintains the desired number of identical pod replicas.

```
1 apiVersion: apps/v1
2 kind: ReplicaSet
```
Show more lines

Example:

Desired replicas = 3
If one pod fails, ReplicaSet creates another pod automatically.
3. Deployment Controller

Manages ReplicaSets and provides:

Rolling updates
Rollbacks
Scaling
```
1 apiVersion: apps/v1
2 kind: Deployment
```
Show more lines

Most commonly used controller for stateless applications.

4. StatefulSet Controller

Used for stateful applications requiring:

Stable hostname
Stable storage
Ordered deployment and scaling

Examples:

MySQL
PostgreSQL
MongoDB
```
1 apiVersion: apps/v1
2 kind: StatefulSet
```
Show more lines
5. DaemonSet Controller

Ensures one pod runs on every node (or selected nodes).

Examples:

Fluentd
Filebeat
Monitoring agents
```
1 apiVersion: apps/v1
2 kind: DaemonSet
```
Show more lines
6. Job Controller

Runs pods until a task completes successfully.

Example:

Database migration
Batch processing
```
1 apiVersion: batch/v1
2 kind: Job
```
Show more lines
7. CronJob Controller

Runs Jobs on a schedule.

Example:

```
1 apiVersion: batch/v1
2 kind: CronJob
```
Show more lines

Schedule:

```
1 schedule: "0 2 * * *"
```
Show more lines

Runs every day at 2 AM.

8. Node Controller

Responsible for:

Monitoring node health
Detecting node failures
Evicting pods from unhealthy nodes

Part of the Kubernetes Controller Manager.

9. EndpointSlice Controller

Maintains EndpointSlice objects for Services and updates backend pod information.

10. Service Account Controller

Creates and manages default service accounts in namespaces.

11. Namespace Controller

Handles:

Namespace creation
Namespace deletion
Resource cleanup
12. Resource Quota Controller

Enforces namespace resource limits.

Example:

```
1 limits.cpu
2 limits.memory
3 pods
4 services
```
Show more lines
13. Horizontal Pod Autoscaler (HPA) Controller

Automatically scales pods based on:

CPU utilization
Memory utilization
Custom metrics
```
1 kubectl autoscale deployment nginx --cpu-percent=80 --min=2 --max=10
```
Show more lines
Kubernetes Controller Manager

Most built-in controllers run inside:

```
1 kube-controller-manager
```
Show more lines

Examples:

Node Controller
Job Controller
ReplicaSet Controller
Deployment Controller
Namespace Controller
ServiceAccount Controller

Check it:

```
1 kubectl get pods -n kube-system
```
Show more lines

or on the control plane node:

```
1 ps -ef | grep kube-controller-manager
```
Show more lines
#### Interview Answer
Kubernetes controllers are control loops that watch cluster resources and reconcile the current state with the desired state. Common controller types include Deployment, ReplicaSet, StatefulSet, DaemonSet, Job, CronJob, Node Controller, Namespace Controller, and HPA Controller. Most built-in controllers run inside the kube-controller-manager component.

## Kubernetes Pod Lifecycle

A Pod goes through several phases from creation to termination.

Pod Lifecycle Flow
```
Pending
   ↓
ContainerCreating
   ↓
Running
   ↓
Succeeded / Failed
   ↓
Terminating
```
1. Pending

The Pod has been accepted by Kubernetes but one or more containers are not yet running.

Activities:

Pod scheduled to a node
Images being pulled
Volumes being mounted

Check:
```
kubectl get pods
```
Example:
```
NAME      READY   STATUS    RESTARTS   AGE
nginx     0/1     Pending   0          5s
```
2. Running

The Pod has been scheduled and at least one container is running.

Example:

```
NAME      READY   STATUS    RESTARTS   AGE
nginx     1/1     Running   0          1m
```
At this stage:

Containers are running
Readiness and liveness probes are monitored
Services can route traffic to the pod (if ready)

3. Succeeded

All containers completed successfully and exited with code 0.

Example:

Backup Job
Batch processing Job
```
STATUS: Succeeded
```
4. Failed

One or more containers terminated with an error.

Reasons:

Application crash
Non-zero exit code
Image issues
Resource problems
```
STATUS: Failed
```
5. Unknown

The Pod state cannot be determined.

Usually occurs when:

Node communication is lost
Kubelet is unreachable
```
STATUS: Unknown
```
### Container States Inside a Pod

Apart from Pod phases, containers have their own states:

Waiting

Container is not yet running.

Examples:
```
ImagePullBackOff
ErrImagePull
CrashLoopBackOff
ContainerCreating
```
Running

Container is executing normally.
```
State: Running
```
Terminated

Container has stopped.
```
State: Terminated
Reason: Completed
Exit Code: 0
```
Pod Termination Process

When you delete a pod:

```
kubectl delete pod nginx
```
Kubernetes performs:

Marks Pod as Terminating
Removes pod from service endpoints
Sends SIGTERM to containers
Waits for terminationGracePeriodSeconds (default 30 sec)
Sends SIGKILL if containers don't stop
Removes Pod object

Pod Conditions

Check using:
```
kubectl describe pod <pod-name>
```
Common conditions:

PodScheduled → Assigned to a node
Initialized → Init containers completed
ContainersReady → All containers ready
Ready → Pod ready to receive traffic

Example:

```
Conditions:
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
```
Lifecycle with Init Containers
```
Pending
   ↓
Init Container 1
   ↓
Init Container 2
   ↓
Application Container
   ↓
Running
```
Init containers must complete successfully before application containers start.

### Interview Answer

The Kubernetes Pod lifecycle consists of phases such as Pending, Running, Succeeded, Failed, and Unknown. A pod starts in the Pending state, moves to Running when scheduled and containers start, and eventually ends in Succeeded or Failed. During deletion, it enters the Terminating state, where Kubernetes gracefully shuts down containers using SIGTERM followed by SIGKILL if necessary. Container states include Waiting, Running, and Terminated.

### A deployment.yaml file is used to create and manage applications in Kubernetes using a Deployment resource.

Step 1: Create deployment.yaml

Example for an Nginx application:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
Step 2: Apply the Deployment
```
kubectl apply -f deployment.yaml
```
Output:
```
deployment.apps/nginx-deployment created
```
Step 3: Verify Deployment

Check deployment:
```
kubectl get deployment
```
Check ReplicaSets:
```
kubectl get rs
```
Check Pods:
```
kubectl get pods
```
How Deployment Creates Pods

When you apply the file:
```
Deployment
    ↓
ReplicaSet
    ↓
Pods
```
For example, with:
```
replicas: 3
```
Kubernetes creates:
```
Deployment (nginx-deployment)
       ↓
ReplicaSet
       ↓
3 Nginx Pods
```
Useful Commands

View deployment details:

```
kubectl describe deployment nginx-deployment
```
Scale deployment:
```
kubectl scale deployment nginx-deployment --replicas=5
```
Update image:

```
kubectl set image deployment/nginx-deployment nginx=nginx:1.25
```
Check rollout status:
```
kubectl rollout status deployment/nginx-deployment

```
Rollback:
```
kubectl rollout undo deployment/nginx-deployment
```
#### Generate a Deployment YAML Automatically

You can generate a starter YAML without creating the deployment:
```
kubectl create deployment nginx \
  --image=nginx \
  --dry-run=client -o yaml > deployment.yaml
```
Then edit the file as needed and apply it:

```
kubectl apply -f deployment.yaml
```
### Interview Answer

A deployment.yaml file defines a Kubernetes Deployment resource. It specifies the application image, number of replicas, labels, selectors, and container configuration. When applied using kubectl apply -f deployment.yaml, Kubernetes creates a Deployment, which manages a ReplicaSet, and the ReplicaSet creates and maintains the required number of Pods.

### Kubernetes Service YAML Workflow

A Service in Kubernetes provides a stable IP/DNS name to access Pods. Since Pods can be created and destroyed frequently, Services act as a consistent endpoint.

Workflow
```
deployment.yaml
      ↓
Deployment
      ↓
ReplicaSet
      ↓
Pods (Dynamic IPs)
      ↓
Service
      ↓
Stable Cluster IP / External IP
      ↓
Users or Applications
```
1. Create Deployment

deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx

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
Apply:
```
kubectl apply -f deployment.yaml
```
Check Pods:
```
kubectl get pods
```
2. Create Service

service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

  type: ClusterIP
```
Apply:
```
kubectl apply -f service.yaml
```
3. How Service Finds Pods

Service uses the selector:
```
selector:
  app: nginx
```
Pod labels:
```
labels:
  app: nginx
```
Kubernetes automatically creates endpoints:
```
Service
   ↓
app=nginx
   ↓
Pod1
Pod2
Pod3
```
4. Verify Service

View Service:

```
kubectl get svc

```
```
NAME            TYPE        CLUSTER-IP      PORT(S)
nginx-service   ClusterIP   10.96.45.123   80/TCP
```
View Endpoints:
```
kubectl get endpoints
```
Output:
```
NAME            ENDPOINTS
nginx-service   10.244.0.5:80,10.244.0.6:80
```
Service Types
ClusterIP (Default)

Internal access only.
```
type: ClusterIP
```
NodePort

Accessible through:
```
<NodeIP>:30080
```
```
type: NodePort

ports:
- port: 80
  targetPort: 80
  nodePort: 30080

```
LoadBalancer

Creates a cloud load balancer.
```
type: LoadBalancer
```
AWS, Azure, and GCP assign an external IP.

### ExternalName

Maps a Kubernetes Service to an external DNS name.

```
type: ExternalName
externalName: google.com
```
End-to-End Example.
```
User Request
      ↓
LoadBalancer Service
      ↓
ClusterIP Service
      ↓
Pod 1
Pod 2
Pod 3
```
If Pod 1 crashes:
```
Pod 1 ❌
Pod 2 ✅
Pod 3 ✅
```
Service automatically routes traffic to healthy Pods.

### Interview Answer

A Kubernetes Service provides a stable network endpoint for a group of Pods. The Service uses label selectors to discover Pods created by a Deployment and load-balances traffic among them. The workflow is: Deployment creates Pods → Service selects Pods using labels → Kubernetes creates Endpoints → Users access the application through the Service's ClusterIP, NodePort, or LoadBalancer.

1. ClusterIP (Default)

A ClusterIP service exposes an application only inside the Kubernetes cluster.

Architecture
```
Pod A
  |
  v
ClusterIP Service
  |
  v
Pod B
```
Example
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```
Access
```
curl http://nginx-service

```
or 
```
curl http://10.96.0.10
```
✅ Accessible from inside cluster only

❌ Not accessible from Internet

Use Cases
Backend APIs
Internal microservices
Database services
2. NodePort

A NodePort exposes a service on a static port of every worker node.

Architecture
```
Internet
   |
   v
NodeIP:30080
   |
   v
NodePort Service
   |
   v
Pod
```
Example
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```
Access
```
http://<Node-IP>:30080
```
Example:
```
http://192.168.1.100:30080
```
NodePort Range

Default:
```
30000 - 32767
```
Use Cases
Development environments
Testing
Small on-prem clusters
Advantages

✅ External access possible

Disadvantages

❌ Need node IP and port

❌ Not ideal for production
3. LoadBalancer

A LoadBalancer service provisions a cloud load balancer and exposes the application to external users.

Architecture
```
Users
  |
  v
Cloud Load Balancer
  |
  v
K8s Service
  |
  v
Pods
```
Example
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-lb
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```
Access
```
http://<External-IP>
Example:
http://52.12.34.56
```
Cloud Providers
AWS ELB/NLB
Azure Load Balancer
Google Cloud Load Balancer
Advantages

✅ Production ready

✅ Automatic load balancing

✅ High availability

✅ Public IP assigned

Disadvantages

❌ Additional cloud cost

Use Cases
Production web applications
APIs
Public-facing services
4. ExternalName

An ExternalName service does not route traffic to Pods.

Instead, it maps a Kubernetes service name to an external DNS name.

Architecture
```
Application
    |
    v
ExternalName Service
    |
    v
external-db.company.com
```
Example
```
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: mydb.company.com
```
Access

Application uses:
```
external-db.default.svc.cluster.local
```
Kubernetes resolves it to:
```
mydb.company.com
```
Use Cases
External databases
SaaS integrations
Legacy systems
Advantages

✅ No proxying

✅ Simple DNS mapping

✅ Easy migration

https://charleswan111.medium.com/kubernetes-services-explained-clusterip-vs-nodeport-vs-loadbalancer-vs-externalname-b540394ef5f4
https://rtfm.co.ua/en/kubernetes-clusterip-vs-nodeport-vs-loadbalancer-services-and-ingress-an-overview-with-examples/

## What is a Service Mesh?

A Service Mesh is a dedicated infrastructure layer that manages communication between microservices.

It handles:

Service-to-service communication
Traffic management
Security (mTLS)
Monitoring and observability
Load balancing
Retry and circuit breaking

Without changing application code.
### What is Istio?

Istio is one of the most popular Service Mesh implementations for Kubernetes.

It adds a sidecar proxy (Envoy) alongside each application pod.
```
+-------------------+
| Application Pod   |
|                   |
| App Container     |
| Envoy Proxy       |
+-------------------+
```
All inbound and outbound traffic passes through Envoy.
## Istio Architecture
```
                 Istio Control Plane
                      (istiod)
                           |
                           |
      -----------------------------------------
      |                                       |
+---------------+                    +---------------+
| Pod A         |                    | Pod B         |
| App           | <--------------->  | App           |
| Envoy Sidecar |                    | Envoy Sidecar |
+---------------+                    +---------------+
```
Components
1. Istiod (Control Plane)

Responsible for:

Service discovery
Configuration management
Certificate management
Traffic policies
2. Envoy Proxy (Data Plane)

Handles:

Routing
Load balancing
TLS encryption
Metrics collection

#### Key Features of Istio
Traffic Management

Can route traffic between versions.

Example:
```
90% → v1
10% → v2
```
Useful for:

Canary deployments
Blue-Green deployments
A/B testing
Security (mTLS)
Encrypts pod-to-pod communication automatically.
```
Service A
   ⇄
mTLS
   ⇄
Service B
```
Benefits:

Encryption
Authentication
Authorization
Observability

Integrates with:

Prometheus
Grafana
Jaeger
Kiali

Provides:

Latency
Error rates
Traffic flow
Distributed tracing
Traffic Resiliency

Supports:

Retries
Timeout
Circuit breaking
Fault injection

Example:
```
retries:
  attempts: 3
```
### How Istio Works
Step 1

Application sends request.
```
App A
  ↓
Envoy A
```
Step 2

Envoy applies policies.
```
Security
Routing
Observability
```
Step 3

Request forwarded.
```
Envoy A → Envoy B
```
Step 4

Destination service receives request.
```
Envoy B → App B
```

### Why Use Istio?
### Without Istio

Every application must implement:

TLS
Retries
Monitoring
Load balancing
With Istio

These capabilities are provided by the service mesh.
```
Application Logic
+
Istio Service Mesh
```
### Interview Answer

A Service Mesh is an infrastructure layer that manages service-to-service communication in a microservices environment. Istio is a popular Service Mesh for Kubernetes that uses Envoy sidecar proxies and the Istiod control plane. It provides traffic management, mTLS security, observability, load balancing, retries, circuit breaking, and canary deployments without requiring changes to application code.
## Ingress and Egress in Kubernetes
1. Ingress (Incoming Traffic)

Ingress controls how external traffic reaches services inside the Kubernetes cluster.

Workflow
```
Internet User
      │
      ▼
Ingress Controller
      │
      ▼
Kubernetes Service
      │
      ▼
Pod
```
Example

Suppose you have an Nginx application:
```
Pod → Service → Ingress
```
Ingress YAML
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
spec:
  ingressClassName: nginx

  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```
Apply:
```
kubectl apply -f ingress.yaml
```
When a user opens:
```
http://app.example.com
```
Traffic flows as:
```
User
  ↓
Ingress Controller
  ↓
nginx-service
  ↓
Nginx Pods
`
```
Popular Ingress Controllers
NGINX Ingress Controller
HAProxy Ingress
Traefik
AWS ALB Controller
Azure Application Gateway Ingress Controller
2. Egress (Outgoing Traffic)

Egress is traffic that leaves a Pod and goes outside the cluster.

Examples:

Pod calling an external API
Pod connecting to a database outside K8s
Pod downloading packages from the internet
Workflow
```
Pod
 ↓
Node Network
 ↓
Internet / External Service
```
Example:
```
Application Pod
      ↓
api.github.com
or
Application Pod
      ↓
Azure SQL Database
```

### Controlling Egress with Network Policies

By default, Pods can usually access external networks.

You can restrict outbound traffic using a NetworkPolicy.

Example

Allow only DNS traffic:
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-egress
spec:
  podSelector: {}

  policyTypes:
  - Egress

  egress:
  - ports:
    - protocol: UDP
      port: 53
```
End-to-End Example

Ingress
```
Browser
   ↓
Ingress
   ↓
Service
   ↓
Pod
```
Egress
```
Pod
   ↓
External API
   ↓
Internet
```
## Interview Answer

Ingress is the incoming traffic to applications running in Kubernetes. It routes external requests through an Ingress Controller to Kubernetes Services and Pods.
 Egress is the outgoing traffic from Pods to external systems such as APIs, databases, or internet services. Kubernetes can control egress traffic using Network Policies, firewalls, or service mesh solutions like Istio.

## Kubernetes Master Node and Worker Node Troubleshooting Guide

1. Master (Control Plane) Node Troubleshooting
Check Control Plane Components
```
1 kubectl get componentstatus
```

Or check pods:

```
kubectl get pods -n kube-system
```

Look for:

kube-apiserver
kube-scheduler
kube-controller-manager
etcd
API Server Issues

Check API Server status:

```
kubectl cluster-info
```

If unreachable:

```
systemctl status kubelet
```
Check logs:

```
journalctl -u kubelet -f
```

Static pod logs:

```
kubectl logs -n kube-system kube-apiserver-<master-node>
```

Common issues:

Certificate expired
API server down
Port 6443 blocked

Verify:
```
netstat -tulnp | grep 6443
```
etcd Issues

Check etcd pod:
```
kubectl get pods -n kube-system | grep etcd
```

View logs:

```
kubectl logs -n kube-system etcd-master
```

Check health:

```
ETCDCTL_API=3 etcdctl endpoint health
```

Common issues:

Disk full
Corrupted database
Certificate issues
Scheduler Issues

Check Scheduler:

```
kubectl get pods -n kube-system | grep scheduler
```

Logs:

```
kubectl logs -n kube-system kube-scheduler-master
```

Symptoms:

```
Pods remain Pending
```

Possible causes:

Resource shortage
Node taints
Affinity rules

Check:

```
kubectl describe pod <pod-name>
```
Controller Manager Issues

Check status:

```
kubectl get pods -n kube-system | grep controller
```

Logs:

```
kubectl logs -n kube-system kube-controller-manager-master
```

Symptoms:

ReplicaSets not creating pods
Nodes not updating
2. Worker Node Troubleshooting
Node Not Ready

Check nodes:

```
kubectl get nodes
```

Output:

```
worker-1 NotReady
```
Detailed information:

```
kubectl describe node worker-1
```
kubelet Issues

Check service:

```
systemctl status kubelet
```

Restart:

```
systemctl restart kubelet
```

View logs:

```
journalctl -u kubelet -f
```

Common issues:

Wrong kubeconfig
Certificate problems
CPU/Memory pressure
Container Runtime Issues

For containerd:

```
systemctl status containerd
```

Restart:

```
systemctl restart containerd
```

Check containers:

```
crictl ps
```

Container runtime info:

```
crictl info
```

Common issues:

Image pull failures
Runtime service down
Disk space exhausted
CNI Network Issues

Check CNI pods:

```
kubectl get pods -n kube-system
```

Example:

```
calico-node

cilium

flannel
```

Check logs:

```
kubectl logs -n kube-system <cni-pod>
```

Verify CNI configuration:

```
ls /etc/cni/net.d/
```

Symptoms:

Pod stuck in ContainerCreating
Pod-to-Pod communication failure
kube-proxy Issues

Check status:

```
kubectl get pods -n kube-system | grep kube-proxy
```

Check logs:

```
kubectl logs -n kube-system kube-proxy-xxxxx
```

Symptoms:

Service not reachable
ClusterIP not working
3. Pod Troubleshooting
Pod Stuck in Pending

Check:

```
kubectl describe pod <pod-name>
```

Common causes:

Insufficient CPU
Insufficient Memory
Taints
PVC not bound
Pod Stuck in ContainerCreating

Check:

```
kubectl describe pod <pod-name>
```

Possible causes:

CNI issue
Volume mount issue
Image pull issue
CrashLoopBackOff

Check logs:

```
kubectl logs <pod-name>
```

Previous crash logs:

```
kubectl logs <pod-name> --previous
```

Common causes:

Application crash
Wrong environment variables
Database connectivity problems
ImagePullBackOff

Describe pod:

```
kubectl describe pod <pod-name>
```

Possible issues:

Wrong image name
Docker Hub access issue
Private registry authentication failure
4. Service Troubleshooting

Check service:

```
kubectl get svc
```
 
Show more lines

Describe:

```
kubectl describe svc nginx-service
```

Verify endpoints:

```
kubectl get endpoints
```

If endpoint is empty:

```
Selector mismatch
```

Check labels:

```
kubectl get pods --show-labels
```
5. DNS Troubleshooting

Test DNS:

```
kubectl run dns-test --image=busybox -it --rm -- nslookup kubernetes.default
```

Check CoreDNS:

```
kubectl get pods -n kube-system | grep coredns
```

Logs:

```
kubectl logs -n kube-system <coredns-pod>
```
## Quick Interview Answer

For master node troubleshooting, I check the health of API Server, Scheduler, Controller Manager, and etcd using kubectl get pods -n kube-system, component logs, and etcd health checks. For worker node troubleshooting, I verify node status, kubelet service, container runtime, CNI plugin, and kube-proxy. For application issues, I use kubectl describe pod, kubectl logs, and check events to identify problems such as Pending, CrashLoopBackOff, ImagePullBackOff, networking issues, or service endpoint mismatches.

## 1. Check All Nodes Resource Usage
```
kubectl top nodes
```

Example output:

```
NAME       CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
worker-1   850m         42%    4200Mi          55%
worker-2   650m         32%    3800Mi          48%
```


Requires Metrics Server to be installed.

2. Check a Specific Node
```
kubectl top node <node-name>
```

Example:

```
kubectl top node worker-1
```
3. Get Detailed Node Information
```
kubectl describe node <node-name>

 
```

Look for:

```
Capacity:

cpu: 4

memory: 8192Mi
Allocatable:

cpu: 3900m

memory: 7800Mi
```

And:
```
Allocated resources:

Resource Requests Limits

cpu 1200m 2400m

memory 2Gi 4Gi
```
4. Check Pod Usage on a Node
```
kubectl top pods -A
```

or

```
kubectl top pods -A --sort-by=cpu
```
```
kubectl top pods -A --sort-by=memory
```
5. Check Node Status
```
kubectl get nodes
```

Example:

```
NAME STATUS ROLES AGE

master-1 Ready control-plane 30d
worker-1 Ready <none> 30d
```
6. From the Node (Linux OS)
```
CPU:
```
```
top
```

or

```
htop
```
```
Memory:
```
```
free -h
```

Disk:

```
df -h
```
### Interview Answer

To check Kubernetes node resource utilization, use kubectl top nodes for CPU and memory consumption. For detailed node capacity, allocatable resources, and pod allocations, use kubectl describe node <node-name>. The Metrics Server must be installed for the kubectl top commands to work.

### Kubernetes Worker Node Commands

These are the most commonly used commands for troubleshooting and managing a Kubernetes worker node.

1. Check Node Status
```
kubectl get nodes
```

Detailed information:

```
kubectl describe node <node-name>
```

Example:

```
kubectl describe node worker-1
```
2. Check Pods Running on a Worker Node

List all pods and their nodes:

```
kubectl get pods -o wide -A
```

Find pods running on a specific node:

```
kubectl get pods -A -o wide | grep worker-1
```
3. Check kubelet Status

Verify kubelet service:

```
systemctl status kubelet
```

Start kubelet:

```
systemctl start kubelet
```
Restart kubelet:

```
systemctl restart kubelet
```

View logs:

```
journalctl -u kubelet -f
```
4. Check Container Runtime
containerd

Status:

```
systemctl status containerd
```

Restart:

```
systemctl restart containerd
```

Logs:

```
journalctl -u containerd -f
```
5. Container Runtime Commands (crictl)

List running containers:

```
crictl ps
```

List all containers:

```
crictl ps -a
```

List images:

```
crictl images
```

Inspect container:

```

crictl inspect <container-id>
```

View logs:

```
crictl logs <container-id>
```
6. Check Node Resources

CPU and Memory:

```
kubectl top node
```

Linux level:

```
top
```
```
htop
```
```
free -h
```
```
df -h
```
7. Check CNI Network

View CNI configuration:

```
ls /etc/cni/net.d/
```

Check network interfaces:

```
ip addr
```

Check routes:

```
ip route
```

Verify CNI pods:

```
kubectl get pods -n kube-system
```
8. Check kube-proxy

Find kube-proxy:

```
kubectl get pods -n kube-system | grep kube-proxy
```

Logs:

```
kubectl logs -n kube-system <kube-proxy-pod>
```
9. Check Node Events
```
kubectl describe node worker-1
```

View events:

```
kubectl get events --sort-by=.metadata.creationTimestamp
```
10. Node Maintenance Commands
Cordoning a Node

Prevent new pods from scheduling:

```
kubectl cordon worker-1
```

Check:

```
kubectl get nodes
```

Output:

```
worker-1 Ready,SchedulingDisabled
```
Uncordon
```
kubectl uncordon worker-1
```
11. Drain a Node

Safely evict workloads before maintenance:

```
kubectl drain worker-1 --ignore-daemonsets
```

After maintenance:
```
kubectl uncordon worker-1
```
12. Worker Node Troubleshooting Commands

Check node status:

```
kubectl get nodes
```

Check pod details:

```
kubectl describe pod <pod-name>
```

Check logs:

```
kubectl logs <pod-name>
```

Check previous logs:

```
kubectl logs <pod-name> --previous
```
Most Important Interview Commands
```
kubectl get nodes
2
kubectl describe node <node-name>
3
kubectl top node
4
systemctl status kubelet
5
journalctl -u kubelet -f
6
systemctl status containerd
7
crictl ps
8
kubectl get pods -A -o wide
9
kubectl cordon <node-name>
10
kubectl drain <node-name> --ignore-daemonsets
11
kubectl uncordon <node-name>
```
### Interview Answer

On a worker node, I typically check node health using kubectl get nodes and kubectl describe node. I verify kubelet and containerd services using systemctl, inspect containers with crictl, monitor resource utilization with kubectl top node, and use kubectl cordon, drain, and uncordon during maintenance activities. These commands help troubleshoot node, pod, networking, and runtime issues effectively.

## How to Troubleshoot kubelet Issues

kubelet is the primary agent running on every Kubernetes worker node. It communicates with the API Server and ensures pods are running as expected.

1. Check Node Status

First identify whether the node is healthy.

```
kubectl get nodes
```

Example:

```
worker-1 NotReady
```

Get detailed information:

```
kubectl describe node worker-1
```

Check for:

MemoryPressure
DiskPressure
PIDPressure
NetworkUnavailable
Ready=False
2. Verify kubelet Service Status

Check if kubelet is running:

```
systemctl status kubelet
```

Healthy output:

```
Active: active (running)
```

If stopped:

```
systemctl start kubelet
```

Restart kubelet:

```
systemctl restart kubelet
```

Enable on boot:
```
systemctl enable kubelet
```
3. Check kubelet Logs

Most kubelet issues can be found in logs.

```
journalctl -u kubelet -f
```

View recent logs:

```
journalctl -u kubelet -n 100
```

Common errors:

API Server Connection Failure
```
Unable to connect to the server
```

Possible causes:

API Server down
Firewall issue
Certificate problem
Certificate Issues
```
certificate has expired
```

Check certificates:

```
openssl x509 -in /var/lib/kubelet/pki/kubelet-client-current.pem -text -noout
```
Resource Pressure
```
NodeHasMemoryPressure
```

Check memory:

```
free -h
```

Check CPU:

```
top
```

Check disk:

```
df -h
```
4. Verify kubelet Configuration

View kubelet configuration:

```
cat /var/lib/kubelet/config.yaml
```

Check kubeconfig:

```
cat /etc/kubernetes/kubelet.conf
```

Validate kubelet startup arguments:

```
ps -ef | grep kubelet
```
5. Verify Communication with API Server

Check API Server accessibility:

```
curl -k https://<api-server-ip>:6443/version
```

Or:

```
kubectl cluster-info
```

Common issue:

```
connection refused
```

Possible causes:

API Server down
Security group/firewall block
DNS issue
6. Check Container Runtime

kubelet depends on container runtime.

For containerd:

```
systemctl status containerd
```

Restart if needed:

```
systemctl restart containerd
```

Verify runtime:

```
crictl info
```

Common error:

```
failed to connect runtime
```
7. Check Pod Status

If kubelet is unhealthy, pods may fail.

```
kubectl get pods -A -o wide
```

Describe problematic pod:

```
kubectl describe pod <pod-name>
```

Look at Events section.

8. Check CNI Network Issues

Pods stuck in ContainerCreating often indicate CNI problems.

Check network plugins:

```
kubectl get pods -n kube-system
```

Examples:

```
calico-node
2
cilium
3
flannel
Show more lines

Check logs:

Shell
1
kubectl logs -n kube-system <cni-pod>
```
9. Validate Node Registration

Check whether kubelet registered the node.

```
kubectl get nodes
```

If missing:

Check logs:

```
journalctl -u kubelet | grep register
```

Common errors:

```
Failed to register node
```

Causes:

Invalid kubeconfig
Certificate issue
API server issue
10. Common kubelet Problems and Fixes
Node NotReady

Check:

```
kubectl describe node <node>
2
journalctl -u kubelet -f
```

Possible causes:

kubelet stopped
container runtime down
network issue
Pods Stuck in Pending

Check:

```
kubectl describe pod <pod>
```

Possible causes:

Insufficient resources
Taints
Scheduler issue
Pods Stuck in ContainerCreating

Check:

```
kubectl describe pod <pod>
```

Possible causes:

CNI failure
Volume mount issue
Image pull problem
CrashLoopBackOff

Check logs:

```
1
kubectl logs <pod>
2
kubectl logs <pod> --previous
```

Possible causes:

Application crash
Configuration issue
Quick Troubleshooting Commands
```
kubectl get nodes
2
kubectl describe node <node>
3
systemctl status kubelet
4
systemctl restart kubelet
5
journalctl -u kubelet -f
6
systemctl status containerd
7
crictl info
8
df -h
9
free -h
10
kubectl get pods -A -o wide
11
kubectl describe pod <pod>
12
 
```
### Interview Answer

When troubleshooting kubelet issues, I first check the node status using kubectl get nodes and kubectl describe node. Then I verify the kubelet service with systemctl status kubelet and review logs using journalctl -u kubelet -f. I also validate connectivity to the API Server, check the container runtime (containerd), inspect CNI networking, and review pod events. Most kubelet problems are related to API Server connectivity, expired certificates, resource pressure, container runtime failures, or networking issues.

## Deployment YAML Explained
Example:
```
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment
  labels:
    app: nginx

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80

        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```
apiVersion
```
apiVersion: apps/v1
```

Specifies the Kubernetes API version used.

Examples:

```
apps/v1
2
v1
3
batch/v1
4
networking.k8s.io/v1
```

For Deployment:

```
1
apps/v1
```
kind
```
kind: Deployment
```
Defines the object type.

Examples:

```
Pod
2
Deployment
3
Service
4
ConfigMap
5
Ingress
6
Secret
```
metadata

Stores information about the object.

```
metadata:
2
name: nginx-deployment
```

Deployment name.

Check using:

```
kubectl get deployment
```

Output:

```
nginx-deployment
```
labels
```
labels:
2
app: nginx
```

Key-value pairs used for identification.

Example:

```
app: nginx
2
environment: prod
3
tier: frontend
```

Check:

```
kubectl get pods --show-labels
```
spec Section

Defines desired state.

```
spec:
```
replicas
```
replicas: 3
```

Number of Pod copies.

Result:

```
nginx-pod-1
2
nginx-pod-2
3
nginx-pod-3
```

Scale manually:

```
kubectl scale deployment nginx-deployment --replicas=5
```
selector
```
selector:
2
matchLabels:
3
app: nginx
```

Deployment manages Pods matching these labels.

Pod label:

```
app: nginx
```

must match.

template

Blueprint for creating Pods.

```
template:
```

Everything inside template becomes the Pod specification.

template.metadata.labels
```
metadata:
2
labels:
3
app: nginx
```

Pod labels.

Service uses these labels to find Pods.

template.spec

Pod definition begins here.

```
spec:
```
containers
```
containers:
```

List of containers in Pod.

Example:

```
containers:
2
- name: nginx
3
image: nginx
4
 
```

One Pod can run multiple containers.

name
```
name: nginx
```

Container name.

image
```
image: nginx:1.25
```

Container image.

Examples:

```
nginx
2
nginx:latest
3
tomcat:9
4
redis:7
```

Kubernetes pulls image from registry.

ports
```
ports:
2
- containerPort: 80
3
``
```

Application listens on port 80 inside container.

Not exposed externally.

resources

Controls CPU and Memory usage.

```
resources:
```
Requests

Minimum resources required.

```
requests:
2
cpu: "100m"
3
memory: "128Mi"
```

Meaning:

```
CPU = 0.1 Core
2
Memory = 128 MB
```

Scheduler uses requests.

Limits

Maximum resources allowed.

```
limits:
2
cpu: "500m"
3
memory: "512Mi"
```

Meaning:

```
CPU = 0.5 Core
2
Memory = 512 MB
```

Container cannot exceed limits.

### Service YAML Explained
Example:
```
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

  type: ClusterIP
```
apiVersion
```
apiVersion: v1
```

Service belongs to core API group.

kind
```
kind: Service
```

Creates a Service object.

metadata
```
metadata:
2
name: nginx-service
```

Service name.

Access internally:

```
http://nginx-service
```
spec.selector
```
selector:
2
app: nginx
```

Finds pods with label:

```
app: nginx
```

Example Pod:

```
labels:
2
app: nginx
```

Service sends traffic to these Pods.

ports

Defines port mapping.

```
ports:
```
protocol
```
protocol: TCP
```

Network protocol.

Options:

```
TCP
2
UDP
3
SCTP
```
port
```
port: 80
```

Service port.

Client accesses:

```
http://service-name:80
```
targetPort
```
targetPort: 80
```

Port inside Pod container.

Flow:
```
Client
   |
Service Port 80
   |
Target Port 80
   |
Pod
```
Example:
```
port: 80
targetPort: 8080
```
Traffic Flow:
```
Service:80 --> Pod:8080
```
type
ClusterIP
```
type: ClusterIP
```

Internal access only.

NodePort
```
type: NodePort
```
```
ports:
2
- port: 80
3
targetPort: 80
4
nodePort: 30080
```

Access:

```
<NodeIP>:30080
```
LoadBalancer
```
type: LoadBalancer
```

Creates cloud load balancer.
```
Internet
   |
Load Balancer
   |
Service
   |
Pods
```
ExternalName
YAML
1
type: ExternalName
2
externalName: google.com
Show more lines

Maps service to external DNS.

### Deployment + Service Relationship
```
Deployment
    |
Creates
    v
Pods
    |
Labels
    v
app=nginx
    |
Service Selector
    v
app=nginx
    |
Traffic Forwarded
    v
Pods
```
### Interview Answer

A Deployment YAML defines how applications run in Kubernetes, including replicas, container images, labels, selectors, resource requests, and limits. A Service YAML exposes those Pods using selectors and provides stable networking. The Service forwards traffic from the service port to the target port of matching Pods, while the Deployment ensures the desired number of Pods are always running.

## PV, PVC, and Volume Claim in Kubernetes

Kubernetes uses Persistent Volumes (PV) and Persistent Volume Claims (PVC) to provide persistent storage for Pods.

Why Do We Need PV and PVC?

Without persistent storage:
```
Pod → Data Stored
Pod Deleted → Data Lost
```
With PV and PVC:
```
Pod → PVC → PV → Storage
```
Data remains even if the Pod is recreated.
### Components
1. Persistent Volume (PV)

A PV is a storage resource in the cluster.

Example:
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv

spec:
  capacity:
    storage: 5Gi

  accessModes:
    - ReadWriteOnce

  hostPath:
    path: /data
```
Create 
```
kubectl apply -f pv.yaml
```
2. Persistent Volume Claim (PVC)

A PVC is a request for storage by a Pod.

Example:
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 2Gi
```
create 
```
kubectl apply -f pvc.yaml
```
Kubernetes automatically binds:
```
PVC (2Gi)
    ↓
PV (5Gi)
```
3. Use PVC in a Pod
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod

spec:
  containers:
  - name: nginx
    image: nginx

    volumeMounts:
    - name: storage
      mountPath: /usr/share/nginx/html

  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: my-pvc
```
Apply 
```
kubectl apply -f pod.yaml
```
Complete Workflow
Step 1

Admin creates PV:
```
PV = 10Gi
```
Step 2

Developer creates PVC:
```
PVC requests = 5Gi
```
Step 3

Kubernetes binds PVC to PV:
```
PVC → PV
```
Step 4

Pod mounts PVC:
```
Pod
 ↓
PVC
 ↓
PV
 ↓
Storage
```
Check Status

View PV:
```
kubectl get pv
```
Output
```
NAME     CAPACITY ACCESS MODES STATUS
my-pv    5Gi      RWO          Bound
```
View PVC:
```
kubectl get pvc
```
output
```
NAME      STATUS  VOLUME
my-pvc    Bound   my-pv
```
Describe PVC:
```
kubectl describe pvc my-pvc
```
### Access Modes
ReadWriteOnce (RWO)

One node can mount the volume in read-write mode.
```
accessModes:
- ReadWriteOnce
```
ReadOnlyMany (ROX)

Multiple nodes can read.
```
accessModes:
- ReadOnlyMany
```
ReadWriteMany (RWX)

Multiple nodes can read and write.
```
accessModes:
- ReadWriteMany
```
Static vs Dynamic Provisioning
Static Provisioning

Admin creates PV manually.
```
PV Created
    ↓
PVC Requests
    ↓
PV Bound

```
Dynamic Provisioning

Uses a StorageClass.
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/aws-ebs
```
PVC 
```
spec:
  storageClassName: fast-storage

```
Kubernetes automatically creates the PV.
## Interview Answer

A Persistent Volume (PV) is a storage resource available in a Kubernetes cluster, while a Persistent Volume Claim (PVC) is a request for storage made by a Pod. Kubernetes binds a PVC to a suitable PV based on size and access mode. The Pod uses the PVC to access storage without knowing the underlying storage details. This separation allows storage to persist even when Pods are deleted or recreated.


## ConfigMaps, Secrets, and Certificates in Kubernetes

These are commonly used to manage configuration, sensitive data, and secure communication in Kubernetes.

## 1. ConfigMap

A ConfigMap stores non-sensitive configuration data as key-value pairs.

Examples:

Application settings
URLs
Environment variables
Feature flags
ConfigMap Example
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config

data:
  APP_ENV: production
  APP_PORT: "8080"
  DB_HOST: mysql-service
```
Use ConfigMap in a Pod
```
env:
  - name: APP_ENV
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_ENV
```
Verify
```
kubectl get configmap
kubectl describe configmap app-config
```
Use Case

Store items like:
```
Database Host
Application Port
Environment Name
API URLs
```
✅ Visible in plain text

✅ Easy to update

❌ Not for passwords
## 2. Secret

A Secret stores sensitive information.

Examples:

Passwords
API Keys
Database credentials
Tokens
SSH keys
Secret Example

Create secret:

```
kubectl create secret generic db-secret \
--from-literal=username=admin \
--from-literal=password=Password123
```

Or YAML:
```
apiVersion: v1
kind: Secret
metadata:
  name: db-secret

type: Opaque

data:
  username: YWRtaW4=
  password: UGFzc3dvcmQxMjM=
```
(Base64 encoded)

Use Secret in Pod
```
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: username

- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password
```
Verify
```
kubectl get secrets
kubectl describe secret db-secret
```
Decode:
```
echo "YWRtaW4=" | base64 -d
```
Output:
```
admin
```
Secret Types
```
Opaque
kubernetes.io/tls
kubernetes.io/dockerconfigjson
kubernetes.io/service-account-token
```
✅ Used for sensitive data

✅ Can be mounted as files or environment variables

```
### ConfigMap vs Secret

Feature 

ConfigMap 

Secret 

****Purpose****
Non-sensitive dataSensitive data
****Storage****
Plain textBase64 encoded
****Examples****
URLs, PortsPasswords, Tokens
****Security****
LowHigher
****Environment Variables****
YesYes
```
### Interview Answer

ConfigMaps are used to store non-sensitive configuration data such as URLs, ports, and application settings. Secrets are used to store sensitive data like passwords, API keys, and tokens. Both can be consumed by Pods as environment variables or mounted volumes.

## 3. Certificates in Kubernetes

Certificates are used to secure communication between Kubernetes components.

Why Certificates?

Kubernetes components communicate over HTTPS.
```
kubectl
   |
TLS Certificate
   |
API Server
```
Certificates provide:

Authentication
Encryption
Secure communication
Important Certificates
API Server Certificate

Used by:
```
kubectl --> API Server
```
Location:
``` /etc/kubernetes/pki/apiserver.crt ```
CA Certificate

Cluster Root Certificate Authority.

Location:
```
/etc/kubernetes/pki/ca.crt
```
Used to sign all cluster certificates.
## Kubelet Certificate

Used by worker nodes to authenticate with API Server.

Location:
```
/var/lib/kubelet/pki/
```
#### etcd Certificate

Secures communication with etcd.

Location:
```
/etc/kubernetes/pki/etcd/
```
#### Check Certificate Expiry

Using kubeadm:
```
kubeadm certs check-expiration
```
Example Output:
```
CERTIFICATE                EXPIRES
apiserver                  2027-01-10
etcd-server                2027-01-10
```
Renew Certificates
```
kubeadm certs renew all
```
Restart services:
```
systemctl restart kubelet
```
TLS Secret Example

Store SSL certificate in Kubernetes:
```
kubectl create secret tls nginx-tls \
  --cert=tls.crt \
  --key=tls.key
```
YAML
```
apiVersion: v1
kind: Secret
metadata:
  name: nginx-tls
type: kubernetes.io/tls
data:
  tls.crt: <base64-cert>
  tls.key: <base64-key>
```
Used by Ingress:
```
tls:
- hosts:
  - app.example.com
  secretName: nginx-tls
```
### Real-Time Example

Suppose your application needs:

ConfigMap
```
DB_HOST=mysql-service
2
APP_ENV=prod
```
Secret
```
DB_USERNAME=admin
2
DB_PASSWORD=Password123
```
TLS Certificate
```
app.company.com
```

HTTPS access for users through Ingress.

### Interview Answer

In Kubernetes, ConfigMaps store non-sensitive configuration such as environment variables, ports, and URLs. Secrets store sensitive data like passwords, API keys, and tokens, which can be mounted into Pods securely. Certificates are used for TLS authentication and encryption between Kubernetes components such as the API Server, kubelets, and etcd, and are also commonly stored as TLS Secrets for HTTPS-enabled applications and Ingress resources.

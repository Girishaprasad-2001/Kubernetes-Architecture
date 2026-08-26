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

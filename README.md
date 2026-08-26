# Kubernetes-Architecture
Kubernetes architecture consists of a Control Plane and Worker Nodes.
                Control Plane
+-----------------------------------+
| API Server                        |
| Scheduler                         |
| Controller Manager                |
| etcd                              |
+-----------------------------------+
               |
               |
      -------------------
      |                 |
+------------+   +------------+
| Worker     |   | Worker     |
| Node 1     |   | Node 2     |
+------------+   +------------+
| kubelet    |   | kubelet    |
| kube-proxy |   | kube-proxy |
| Pods       |   | Pods       |
+------------+   +------------+

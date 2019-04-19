## Kubernetes

### 基本概念

![img](https://yeasy.gitbooks.io/docker_practice/content/kubernetes/_images/kubernetes_design.jpg)

- 节点（`Node`）：一个节点是一个运行 Kubernetes 中的主机，节点可以是依赖于一个集群环境的虚拟机或者物理机器。每个节点都有一些必要的服务以运行容器组，并且它们都可以通过主节点来管理。
- 容器状态：描述节点的当前状态
  - 主机 IP
  - 节点周期：`Pending`，`Running`，`Terminated`
  - 节点状态：Running 中的状态，有 `NodeReachable`和 `NodeReady`
- 容器组（`Pod`）：一个 Pod 对应于由若干容器组成的一个容器组，同个组内的容器共享一个存储卷(volume)。容器组被调度到一组节点运行，直到容器的生命周期结束或者其被删除。容器组主要是为了数据共享和它们之间的通信。在一个容器组中，容器都使用相同的网络地址和端口，可以通过本地网络来相互通信。每个容器组都有独立的 IP，可用通过网络来和其他物理主机或者容器通信。
- 容器组的生命状态：：`pending`、`running`、`succeeded`、`failed`
- 容器组生命周期（`pos-states`）：包含所有容器状态集合，包括容器组状态类型，容器组生命周期，事件，重启策略，以及 replication controllers。
- Replication Controllers：主要负责指定数量的 pod 在同一时间一起运行。
- 服务（`services`）：一个 Kubernetes 服务是容器组逻辑的高级抽象，同时也对外提供访问容器组的策略。
- 卷（`volumes`）：一个卷就是一个目录，容器对其有访问权限。
- 标签（`labels`）：标签是用来连接一组对象的，比如容器组。标签可以被用来组织和选择子对象。
- 接口权限（`accessing_the_api`）：端口，IP 地址和代理的防火墙规则。
- web 界面（`ux`）：用户可以通过 web 界面操作 Kubernetes。
- 命令行操作（`cli`）：`kubecfg`命令。



### 架构

![Kubernetes æ¶æ](https://yeasy.gitbooks.io/docker_practice/content/kubernetes/_images/k8s_architecture.png)

Kubernetes 首先是一套分布式系统，由多个节点组成：

- 管理平面的主节点/控制节点（Master Node）
  - `apiserver` 是整个系统的对外接口，提供一套 RESTful 的 [Kubernetes API](https://github.com/kubernetes/kubernetes/tree/master/docs/api-reference)，供客户端和其它组件调用；
  - `scheduler` 负责对资源进行调度，分配某个 pod 到某个节点上。是 pluggable 的，意味着很容易选择其它实现方式；
  - `controller-manager` 负责管理控制器，包括 endpoint-controller（刷新服务和 pod 的关联信息）和 replication-controller（维护某个 pod 的复制为配置的数值）。
- 运行平面的工作节点（Worker Node），提供稳定的操作接口和能力抽象
  - kubelet 是工作节点执行操作的 agent，负责具体的容器生命周期管理，根据从数据库中获取的信息来管理容器，并上报 pod 运行状态等；
  - kube-proxy 是一个简单的网络访问代理，同时也是一个 Load Balancer。它负责将访问到某个服务的请求具体分配给工作节点上的 Pod（同一类标签）

- Etcd：作为数据后端，存储所有的主节点上的状态信息，很容易实现主节点的分布式扩展，组件可以自动的去侦测 Etcd 中的数值变化来获得通知，并且获得更新后的数据来执行相应的操作。



### kubectl

[kubectl](https://github.com/kubernetes/kubernetes) 是 Kubernetes 自带的客户端，可以用它来直接操作 Kubernetes。



### 本地搭建 k8s

[快速上手](https://yeasy.gitbooks.io/docker_practice/content/kubernetes/quickstart.html)

![å¨ Docker ä¸­å¯å¨ Kubernetes](https://yeasy.gitbooks.io/docker_practice/content/kubernetes/_images/k8s-singlenode-docker.png)
# Kubernetes 入门

## Kubernetes 是什么

Kubernetes 是一个全新的基于容器技术的分布式架构领先方案。如果我们的系统设计遵循了 kubernetes 的设计思想，那么传统系统架构中那些和业务没有多大关系的底层代码或功能模块，我们可以将精力更加集中于业务本身。

在 Kubernetes 中，Service 是分布式集群架构的核心，一个 Service 对象拥有如下关键特征：
- 拥有唯一指定的名称（比如 mysql-server）；
- 拥有一个虚拟 IP（Cluster IP、Service IP 或 VIP）和端口号；
- 能够提供某种远程服务能力；
- 被映射到提供这种服务能力的一组容器应用上；

容器提供了强大的隔离功能，所以有必要把为 Service 提供服务的这组进程放进容器中进行隔离。为此，Kubernetes 设计了 Pod 对象，将每个服务进程都包装进相应的 Pod 中，使其成为在 Pod 中运行的一个容器（Container）。

Pod 运行在一个被称为节点（Node）的环境中，这个节点既可以是物理机，也可以是私有云或者公有云中的一个虚拟机，通常在一个节点上运行几百个 Pod；其次，在每个 Pod 中都运行着一个特殊的被称为 Pause 的容器，其他的容器则为业务容器，这些业务容器共享 Pause 容器的网络栈和 Volume 挂载卷，因此它们之间的通信和数据交换更为高效，在设计时我们可以充分利用这一特性将一组密切相关的服务进程都放入同一个 Pod 中；

在集群管理方面，Kubernetes 将集群中的机器划分为一个 Master 和一些 Node。在 Master 上运行着集群管理相关的一组进程 kube-apiserver、kube-controller-manager 和 kube-scheduler，这些进程实现了整个集群的资源管理、Pod 调度、弹性伸缩、安全控制、系统监控和纠错等管理功能，并且都是自动完成的。Node 作为集群中的工作节点，运行真正的应用程序，在 Node 上 Kubernetes 管理的最小运行单元是 Pod。在 Node 上运行着 Kubernetes 的 kubelet、kube-proxy 服务进程，这些进程负责 Pod 的创建、启动、监控、重启、销毁，以及实现软件模式的负载均衡器。
# K8S

## Kubernetes Clusters

- k8s 以高效能的方式自动分配和调度整个集群中的容器应用，是一个可用于生产的开源平台。
- 一个 k8s 集群中包含以下两种类型的资源
  - Master: 集群的调度主程序；
  - Nodes: 集群中的工作节点，用于运行应用；（节点中也有操作容器的工具，e.g docker）
- 当你使用 k8s 发布应用时，需要告知 Master 启动应用容器。Master 开始调度容器运行在 Node 节点中，这些 Node 节点可以使用 K8S 的 API 在集群中进行通信和交互。

- 查看 k8s 版本信息
```bash
kubectl version
# Client Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.7", GitCommit:"8fca2ec50a6133511b771a11559e24191b1aa2b4", GitTreeState:"clean", BuildDate:"2019-09-18T14:47:22Z", GoVersion:"go1.12.9", Compiler:"gc", Platform:"darwin/amd64"}
# Server Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.7", GitCommit:"8fca2ec50a6133511b771a11559e24191b1aa2b4", GitTreeState:"clean", BuildDate:"2019-09-18T14:39:02Z", GoVersion:"go1.12.9", Compiler:"gc", Platform:"linux/amd64"}
# Client Version 是 kubectl 的版本信息
# Server Version 是 Kubernetes 的版本信息
```

- 查看 k8s 集群信息
```bash
kubectl cluster-info
```

- 查看集群中的节点信息
```bash
kubectl get nodes
```

## Using Kubectl to Create a Deployment

- 首先编辑一份 Kubernetes 部署配置文件，然后 k8s 将会根据这份配置文件启动和更新实例，调度各个应用实例到集群的各个节点上。
- 应用实例被创建后，k8s 的部署控制器会持续监控这些实例。一旦发现实例出现故障或者被删除了，控制器会马上使用另一个节点上的实例替换该实例，使应用保持在一个健康的状态。

- 创建一个 deployment

```bash
# kubernetes-bootcamp 是 deployment 的名称
# --image=grc.io/google-smaples/kubernetes-bootcamp:v1  是应用镜像的地址
kubectl create deployment kubernetes-bootcamp --image=grc.io/google-smaples/kubernetes-bootcamp:v1
```

上面这条命令所做的事情：
  - 为应用程序实例的运行搜索一个合适的节点；
  - 调度应用运行在该节点上；
  - 配置集群以便在需要时重新调度一个新节点上的实例；

- 获取 deployments

```bash
kubectl get deployments
```

k8s 内部运行的 pod 在一个独立的网络环境中运行。默认来说他们可以对同一个集群的其他 pod 和 service 处于可见状态，但是在网络之外不可见。我们可以使用 kubectl 提供的 API 在应用之间通信。
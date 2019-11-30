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

## Pods

- 当你创建了一个 Depolyment 后，k8s 会创建一个 Pod 用于承载你的应用实例。
- Pods 是 K8S 平台的原子单元，每一个 Pod 都将绑定一个 Node；当一个 Node 挂掉后，集群会调度可用的 Node 上一个 Pod 替换它；
- Pod 永远运行在 Node 上。每个 Node 都被 Master 所管理。一个 Node 可以有多个 Pod，并且 K8S 会自动处理跨集群 Nodes 调度 Pods 的问题。
- 每一个 K8S Node 至少要运行：
  - Kubelet，它管理Pods和在机器上运行的容器。
  - 一个容器工具，用于拉取镜像；

![图示](http://shadows-mall.oss-cn-shenzhen.aliyuncs.com/images/blogs/other/Jietu20191129-174359.png)

- 针对 Pod 的一些指令
```bash
# 获取现有的 pods
kubectl get pods

# 显示 pods 的详细信息
kubectl describe pods

# Pods 在隔离的专用网络中运行，因此我们需要使用代理与其交互
# 开启代理
kubectl proxy

# 暴露环境变量
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')

# 输出获取的环境变量
echo Name of the Pod: $POD_NAME

# 使用 curl 访问代理
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/

# 输出 pod 的日志
kubectl logs $POD_NAME

# 在 Pod 中执行命令 —— 输出环境变量
kubectl exec $POD_NAME env

# 进入 Pod 内部 bash
kubectl exec -ti $POD_NAME bash
cat server.js
curl localhost:8080
exit
```

## Using a Server to Expose Your App

- 前端程序不应该关注后台的自动调度，包括 Pod 的丢失和重新创建。每一个 Pod 都运行在一个隔离的网络环境，即使是同一个 Node 节点上的 Pods 也是如此。
- Service 是 K8S 中的一个抽象，定义了访问和设置 Pods 的逻辑策略。Service 使不同的 Pods 之间达到松耦合的效果。Service 可以通过该 YAML 或 JSON 来进行配置。
- 由于每个 Pod 都有一个单独的 IP 地址，如果没有 Service 的话，这些 IP 不会被暴露在集群中。Service 可以让你的应用进行数据传输，Service 可以通过下面几种方式来暴露你的
  - ClusterIP（默认）：在集群中暴露一个内部 IP 地址，只能在内部集群访问；
  - NodePort：在每个节点的相同端口暴露服务；
  - LoadBalancer
  - ExternalName

![图示](http://shadows-mall.oss-cn-shenzhen.aliyuncs.com/images/blogs/other/Jietu20191130-111548.png)

Service 在一组 Pod 之间传输数据；
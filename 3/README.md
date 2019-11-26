# 组件说明

- 高可用集群副本数据最好是 >=3 奇数个
- API SERVER
  - 所有服务访问的统一入口
- ControllerManager
  - 维持副本期望数目
- Scheudler
  - 负责接受任务，选择合适的节点进行分配任务；
- ETCD
  - 键值对数据库；
  - 存储 K8S 集群所有重要信息（持久化）；
- Kubelet
  - 直接跟容器引擎交互实现容器的生命周期管理；
- Kube-proxy
  - 负责写入规则至 IPTABLES、IPVS 实现服务映射访问的目的；
- COREDNS
  - 可以为集群中的 SVC 创建一个域名 IP 的对应关系解析；
- DASHBOARD
  - 给 K8S 集群提供一个 B/S 结构访问体系；
- INGRESS CONTROLLER
  - 官方只能实现四层代理，INGRESS 可以实现七层代理
- FEDETATAION
  - 提供一个可以跨集群中心多 K8S 统一管理功能；
- PROMETHEUS
  - 提供 K8S 集群的的监控能力
- ELK
  - 提供 K8S 集群日志统一分析接入平台
# Docker 容器间通信方法

Docker 容器间的通信方式根据媒介可以分为：volume 共享通信、网络通信等；根据通信范围也可以分为：同主机通信和跨主机通信等；

## Docker 的网络驱动模型

Docker 的网络驱动模型分类：
- bridge: Docker 中默认的网络驱动模型，通常用于同一主机内的多个独立容器之间通信；
- host：打破 Docker 容器与宿主机之间的网络隔离，直接使用宿主机的网络环境；
- overlay：可以连接多个 docker 守护进程或者满足集群服务之间的通信；适用于不同宿主机上的 docker 容器之间的通信；
- macvlan：可以为 docker 容器分配 MAC 地址，使其像真实的物理机一样运行；
- none：禁用网络驱动，需要自己手动自定义网络驱动配置；

### bridge

![bridge 模式](http://shadows-mall.oss-cn-shenzhen.aliyuncs.com/images/blogs/other/Jietu20191130-122831.png)

当 Docker 启动时会自动在主机创建一个虚拟网桥 docker0，bridge 驱动模式中，创建 docker 容器时会自动创建一对 veth pair 接口。就像是一对靠虚拟网线连接起来的两个虚拟网卡，一端连接着 docker 容器，一端连接着虚拟网桥 docker0；

通过这种方式，不同 docker 容器之间可以通过 IP 地址互相通信，也可以通过虚拟网桥访问主机上的网络 `eth0`；
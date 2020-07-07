## Kubernetes架构


![NzuQ5n.png](https://s1.ax1x.com/2020/07/04/NzuQ5n.png)
<center>k8s架构图</center>

Kubernetes主要由以下几个核心组件组成：

* etcd保存了整个集群的状态；
* apiserver提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制；
* controller-manager负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
* scheduler负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上；
* kubelet负责维护容器的生命周期，同时也负责Volume（CSI）和网络（CNI）的管理；
* Container runtime负责镜像管理以及Pod和容器的真正运行（CRI）；
* kube-proxy负责为Service提供cluster内部的服务发现和负载均衡；

除了核心组件，还有一些推荐的插件，其中有的已经成为CNCF中的托管项目：

* CoreDNS负责为整个集群提供DNS服务
* Ingress Controller为服务提供外网入口
* Prometheus提供资源监控
* Dashboard提供GUI
* Federation提供跨可用区的集群


## Kubernetes架构示意图

### 整体架构

下图清晰表明了Kubernetes的架构设计以及组件之间的通信协议。

![NzuLZQ.png](https://s1.ax1x.com/2020/07/04/NzuLZQ.png)
<center>k8s架构图（图片来源于网络）</center>


![NzmlZj.png](https://s1.ax1x.com/2020/07/04/NzmlZj.png)


Kubernetes（简称为 K8s），最初由 Google 的工程师开发和设计。Kubernetes 是用于自动部署、扩展和管理容器化应用程序的开源系统，它旨在提供跨主机集群的自动部署、扩展以及运行应用程序容器的平台。Kubernetes 支持一系列容器工具, 包括 Docker 等。

本文主要讲述 Kubernetes 如何从 Google 的内部容器编排方案发展为今天大家熟知的开源容器集群解决方案的历史和相关里程碑。

## 2003-2004: Borg 系统的诞生

一个约有 3-4 人、与新版的谷歌新搜索引擎合作小规模项目。 Borg 是一个大规模的内部集群管理系统，它运行着来自数千个不同应用程序的数十万个作业，跨越许多集群，而每个集群拥有多达数万台计算机。

## 2013: 从 Borg 到 Omega

继 Borg 之后，Google 推出了 Omega 集群管理系统，这是一种适用于大型计算集群的灵活、可扩展的调度程序。

## 2014: Google 推出 Kubernetes

2014 年中：Google 推出 Kubernetes （作为 Borg 的开源版本）。

6 月 7 日：初始版本发布——完成 Kubernetes 在 GitHub 上的第一次提交。

7 月 10 日：微软、RedHat、IBM、Docker 加入 Kubernetes 社区。

## 2015: Kubernetes v1.0 和 CNCF 的一年

7 月 21 日：Kubernetes v1.0 发布。 随后，谷歌与 Linux 基金会合作组建了云原生计算基金会（CNCF）。CNCF 旨在建立可持续的生态系统，并围绕一系列高质量项目建立一个社区。

11 月 3 日：Kubernetes 生态系统继续增长！Deis、OpenShift、华为和 Gondor 陆续加入。

11 月 5 日：Kubernetes 1.1 进行主要的性能升级、改进的工具和新功能，让应用程序更易于构建和部署。

11 月 9 日至 11 日：KubeCon 2015 在美国旧金山举办，这是首届 Kubernetes 会议。 会议通过专家技术讲座激发创造力并促进 Kubernetes 普及。

## 2016: Kubernetes 在这一年成为主流

2 月 23 日：Kubernetes 软件包管理系统 Helm 首次发布。

2 月 24 日：继首次会议在 2015 年 11 月举办后，KubeCon EU 2016，首届在欧洲举办的 Kubernetes 会议吸引近 500 名与会者参加。

3 月 16 日：Kubernetes 1.2 发布，改进包括扩展、简化应用程序部署和自动化集群管理等功能。

7 月 1 日：Kubernetes 1.3 发布，连接云原生和企业 Workload。 v1.3 引入 Rktnetes 1.0 和一个新的 Alpha ‘PetSet’ 对象，并支持在多个集群中发现运行的服务。

7 月 11 日：Minikube 正式发布：它是一个可以让 Kubernetes 在本地运行的工具。

9 月 8 日：一个管理生产级 Kubernetes 集群的官方 Kubernetes 项目——Kops 面世。

9 月 19 日：Monzo 发布了一个关于他们如何使用 Kubernetes 从头开始构建银行系统的案例研究。

9 月 26 日：Kubernetes 1.4 推出了一种新工具 kubeadm，有助于提高 Kubernetes 的可安装性。 此版本提供了更简单的设置，支持集成 Helm 的状态应用以及新的跨集群联合功能。

9 月 29 日：Pokemon GO 是有史以来在 Google 容器引擎上最大的 Kubernetes 部署，它的创作者发布了一个关于他们如何操作 Kubernetes 的案例研究。

11 月 8 日至 9 日：CloudNativeCon + KubeCon 2016 在美国西雅图举办。来自世界各地的 1,000 多名终端用户，主要贡献者和开发人员齐聚一堂，交流有关 Fluentd、Kubernetes、Prometheus、OpenTracing 和其他云原生技术。

12 月 7 日：Kubernetes 发布 Node feature discovery。它能检测 Kubernetes 集群中每个节点上可用的硬件功能，并使用节点标签公布这些功能。

12 月 21 日：Kubernetes 1.5 发布，Kubernetes 得到 Windows 服务器的支持。 新功能包括容器化多平台应用程序，支持 Windows 服务器的容器和 Hyper-V 容器，扩展的应用程序生态系统，覆盖异构的数据中心等等。

12 月 23 日：Kubernetes 支持 OpenAPI，可允许 API 提供商定义他们的操作和模型，开发人员可以自动化他们的工具。

## 2017: 企业采用和支持的一年

3 月 28 日：Kubernetes 1.6 发布。 具体的更新有：默认启用 etcd v3、删除单个容器运行时的直接依赖关系、测试版 RBAC、自动配置 StorageClass 对象。

3 月 29 日至 30 日：CloudNativeCon + KubeCon 在欧洲柏林举办。 来自世界各地的 1,500 名终端用户、主要贡献者和开发者交流了云原生相关知识。

5 月 24 日：Google 与 IBM 发布了一项开放式技术 Istio，它提供了一种对任何平台、来源或是供应商都能无缝连接、管理和保护不同微服务网络的方法。

6 月 30 日：Kubernetes 1.7：容器编排标准添加了本地存储、Secrets 加密和可扩展性，例如：API 聚合、第三方资源、容器运行时接口等。

8 月 16 日：GitHub 开始在 Kubernetes 上运行。所有 Web 和 API 请求都由在 metal cloud 上部署的 Kubernetes 集群中运行的容器提供服务。

8 月 31 日：Kelsey Hightower 发布了 Kubernetes The Hard Way。 Kubernetes The Hard Way 对 Kubernetes 学习进行了优化，学习者需要通过长时间的学习来创建 Kubernetes 集群所需的工作。

9 月 11 日：CNCF 宣布推出首批 Kubernetes 认证服务供应商，有超过 22 家供应商获得首批 Kubernetes 认证。这些预先认证的机构在帮助企业成功运用 Kubernetes 方面有非常丰富的经验。

9 月 13 日：Oracle 以白金会员的身份加入了 CNCF。 Oracle 开源了 Oracle 云基础架构的 Kubernetes 安装程序，并在 Oracle Linux 上发布了 Kubernetes。

9 月 29 日：Kubernetes 1.8 发布，这个版本是基于角色访问控制（RBAC）授权程序的里程碑，这是一种用于控制对 Kubernetes API 的访问的机制。

10 月：Docker 拥抱 Kubernetes。开发人员和运营商可以使用 Docker 创建应用程序，并使用 Docker Swarm 和 Kubernetes 进行无缝地测试和部署。

10 月 17 日：Docker 平台和 Moby Projekt 添加 Kubernetes，客户和开发人员可以选择使用 Kubernetes 和 Swarm 来协调容器工作负载。

10 月 24 日：微软推出 AKS 预览版 — AKS 具有 Azure 托管控制平面，并能自动升级、自我修复、易于扩展以及为开发人员和集群运营商提供简单的用户体验。客户在零运营开销的情况下能获得开源 Kubernetes 。

11 月 29 日：亚马逊宣布为 Kubernetes 提供弹性容器服务，可在 AWS 上使用 Kubernetes 进行部署、管理和扩展容器化应用程序。

12 月 6 日至 8 日：KubeCon + CloudNativeCon 在美国奥斯汀举办，这次会议聚集了来自世界各地的超过 4,100 名终端用户、供应商、主要贡献者和开发者。 289 场会议、主题演讲以及闪电式演讲。

12 月 15 日：Kubernetes 1.9 发布：Apps Workloads GA 和 Expanded Ecosystem。 新功能包括，apps / v1 Workloads API 的一般可用性、Windows 支持（beta）、存储增强功能等。

12 月 21 日：介绍 Kubeflow ，一个为 Kubernetes 构建的可组合、便携、可扩展的机器学习堆栈。

## 2018：精彩继续

3 月 2 日：Kubernetes 1.10 的第一个测试版发布，使用生产就绪版本用户可以测试 Kubelet TLS Bootstrapping 、API 聚合以及更详细的存储指标。

5 月 1 日：Google 推出由 Craig Box 主持的 Kubernetes Podcast。

5 月 2 日至 4 日：KubeCon + CloudNativeCon Europe 2018 在哥本哈根举办。超过 4300 名开发人员聚集在一起。

5 月 2 日：DigitalOcean 使用 Kubernetes，宣布推出新的托管 Kubernetes 产品。 DigitalOcean Kubernetes 将在其现有的云计算和存储选项上提供容器管理和编排平台作为免费服务。

5 月 4 日：Kubeflow 0.1 发布，它提供了一套尽可能小的软件包来开发、培训和部署 ML。

5 月 21 日：Google Kubernetes Engine 1.10 发布，可供企业使用。其具有共享虚拟私有云、区域永久磁盘、区域集群、节点自动修复和自定义 Pod 自动缩放等功能，以实现更快的自动化。

5 月 24 日：Kubernetes Containerd Integration 进入 GA 阶段。 Containerd 1.1 与 Kubernetes 1.10 及以上版本兼容，并支持所有 Kubernetes 功能。用户可以在生产环境的Kubernetes 集群中使用 Containerd 1.1 作为容器运行时组件。

6 月 5 日：亚马逊 EKS 可用。 Amazon EKS 简化了构建、保护、操作和维护 Kubernetes 集群的过程。让那些专注构建应用程序而不想从头设置 Kubernetes 集群的公司，可以充分利用基于容器的云计算的优势。

6 月 13 日：亚马逊 AKS（Azure Kubernetes Service）可用。 通过 AKS，用户可以部署和管理他们用于生产的 Kubernetes 应用程序，Azure 的工程师为客户的完全托管的 Kubernetes 集群提供持续的监控、操作和支持。

6 月 27 日：Kubernetes 1.11 发布：集群内负载平衡和 CoreDNS 插件达到普遍可用性。这个最新版本在网络方面有关键性功能：开启了 SIG-API Machinery 和 SIG-Node 的两个主要功能，用于 beta 测试，并继续增强存储功能。这些功能一直是过去两个版本的焦点。……

## 2019：飞速发展

## 2020：未来可期



> 本文内容大多来自互联网，如有侵权请联系本人删除，邮箱 <a href="mailto:ruoshuidba@qq.com">ruoshuidba@qq.com</a>
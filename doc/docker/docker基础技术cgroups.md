# Cgroup

cgroups(control group)是Linux内核提供的一种可以限制、记录、隔离进程组（process groups）所使用的物理资源（如：cpu、memory、io等等）的机制。最初由google工程师提出，后来被整合进Linux内核。Cgroups也是LXC为实现虚拟化所使用的资源管理手段。

## Cgroups可以做什么？
&emsp;&emsp; Cgroups最初的目标是为资源管理提供的一个统一的框架。现在适用于多种应用场景，从单个进程的资源控制，到实现操作系统层次的虚拟化（OS Level Virtualization）。Cgroups提供了以下功能：

1. 资源限制（Resource limiting）：可以对任务使用的资源总额进行限制。比如内存上限
2. 优先级分配（Prioritization）：分配CPU时间片数量和磁盘IO带宽大小
3. 记录进程组使用的资源数量（Accounting）：可以使用cpuacct子系统记录某个进程组使用的cpu时间
4. 任务控制（Control）：对任务执行挂起、恢复等操作

## Cgroups相关概念及其关系

## 相关概念

1. task（任务）：task表示系统的一个进程
2. cgroup（控制组）：cgroups中的资源控制都是以cgroup为单位实现。cgroup表示按照某种资源控制标准划分而成的任务组，包含一个或多个子系统。一个任务可以加入某个cgroup，也可以从某个cgroup迁移到另外一个cgroup
3. subsystem（子系统）：subsystem是一个资源调度控制器（Resource Controller）。比如CPU子系统可以控制CPU时间分配，内存子系统可以限制cgroup内存使用量
4. hierarchy（层级树）：hierarchy由一系列cgroup以一个树状结构排列组成，每个hierarchy通过绑定对应的subsystem进行资源调度。hierarchy中的cgroup节点可以保护零或多个子节点，子节点继承父节点的属性。整个系统可以有多个hierarchy

## Cgroups子系统介绍

- blkio：块设备设定输入、输出限制，比如物理设备（磁盘、固态硬盘、USB等）
- cpu：使用调度程序提供对CPU的cgroup任务访问
- cpuacct：用于自动生成cgroup中任务所使用的cpu报告
- cpuset：为cgroup中任务分配独立cpu和内存节点
- devices：可允许或者拒绝cgroup中的任务访问设备
- freezer：用于挂起或者回复cgroup中的任务
- memory：用于设定cgroup中任务使用的内存限制，并自动生成内存资源使用报告
- net_cls：用登记标识符（classid）标记网络数据包
- ns：名称空间



## 参考

https://www.kernel.org/doc/Documentation/cgroup-v1/cgroups.txt


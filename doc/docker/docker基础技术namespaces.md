# Namespace 


## Namespace概述

&emsp;&emsp;Linux内核实现namespace的一个主要目的，就是为了实现轻量级虚拟化（容器）技术服务。在同一个namespace下的进程可以感知到彼此的变化，而对外界的进程一无所知。这样就可以让容器中的进程产生错觉，仿佛自己只剩余一个独立的系统环境中，以达到隔离的目的

&emsp;&emsp; 完成一个基本容器需要六项隔离，Linux内核中提供了六种隔离的系统调用：

| NS | 作用 | 内核版本收录 |
| ------ | ------ | ------ |
| UTS CLONE_NEWUTS | 用于隔离主机名和域名 | since Linux 2.6.19 |
| IPC CLONE_NEWIPC  | 用于隔离信号量，消息队列和共享内存  |  since Linux 2.6.19|
|  PID CLONE_NEWPID | 用于隔离进程编号  |  since Linux 2.6.24 |
| Network CLONE_NEWNET | 用于隔离挂载点（文件系统）| since Linux 2.4.19|
| User CLONE_NEWUSER| 用于隔离用户和用户组  | since Linux 2.6.23 |




相关的系统调用

- `clone()` - 实现线程的系统调用，用来创建一个新的进程
- `unshare()` - 使某进程脱离某个namespace
- `setns()` - 把某进程加入到某个namespace


## 参考

https://lwn.net/Articles/531114/

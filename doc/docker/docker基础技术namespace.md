# Namespace 


&emsp;&emsp; 完成一个基本容器需要六项隔离，Linux内核中提供了六种隔离的系统调用：

1. UTS CLONE_NEWUTS  用于隔离主机名和域名

2. IPC CLONE_NEWIPC  用于隔离信号量，消息队列和共享内存

3. PID CLONE_NEWPID  用于隔离进程编号

4. Network CLONE_NEWNET 用于隔离网络设备、网络栈和端口

5. Mount CLONE_NEWNS 用于隔离挂载点（文件系统）

6. User CLONE_NEWUSER 用于隔离用户和用户组

> Linux内核实现namespace的一个主要目的，就是为了实现轻量级虚拟化（容器）技术服务。在同一个namespace下的进程可以感知到彼此的变化，而对外界的进程一无所知。这样就可以让容器中的进程产生错觉，仿佛自己只剩余一个独立的系统环境中，以达到隔离的目的



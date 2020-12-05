# Cgroup

cgroups(control group)也是Linux内核提供的机制。其功能主要为：

1. 资源限制：可以对任务使用的资源总额进行限制。比如内存上限
2. 优先级分配：分配CPU时间片数量和磁盘IO带宽大小
3. 任务控制：对任务执行挂起、恢复等操作

cgroup相关术语：

1. task（任务）：task表示系统的一个进程
2. cgroup（控制组）：cgroups中的资源控制都是以cgroup为单位实现。cgroup表示按照某种资源控制标准划分而成的任务组，包含一个或多个子系统。一个任务可以加入某个cgroup，也可以从某个cgroup迁移到另外一个cgroup
3. subsystem（子系统）：subsystem是一个资源调度控制器（Resource Controller）。比如CPU子系统可以控制CPU时间分配，内存子系统可以限制cgroup内存使用量
4. hierarchy（层级树）：hierarchy由一系列cgroup以一个树状结构排列组成，每个hierarchy通过绑定对应的subsystem进行资源调度。hierarchy中的cgroup节点可以保护零或多个子节点，子节点继承父节点的属性。整个系统可以有多个hierarchy
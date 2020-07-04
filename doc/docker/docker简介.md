
## 了解Docker的前生LXC

&emsp;&emsp;LXC为Linux Container的简写。可以提供轻量级的虚拟化，以便隔离进程和资源，而且不需要提供指令解释机制以及全虚拟化的其他复杂性。相当于C++中的NameSpace。容器有效地将由单个操作系统管理的资源划分到孤立的组中，以更好地在孤立的组之间平衡有冲突的资源使用需求。

与传统虚拟化技术相比，它的优势在于：
*（1）与宿主机使用同一个内核，性能损耗小；
*（2）不需要指令级模拟；
*（3）不需要即时(Just-in-time)编译；
*（4）容器可以在CPU核心的本地运行指令，不需要任何专门的解释机制；
*（5）避免了准虚拟化和系统调用替换中的复杂性；
*（6）轻量级隔离，在隔离的同时还提供共享机制，以实现容器与宿主机的资源共享。


## LXC与docker什么关系？
docker并不是LXC替代品，docker底层使用了LXC来实现，LXC将linux进程沙盒化，使得进程之间相互隔离，并且能够课哦内阁制各进程的资源分配。在LXC的基础之上，docker提供了一系列更强大的功能。


## 什么是docker
&emsp;&emsp; docker是一个开源的应用容器引擎，基于go语言开发并遵循了apache2.0协议开源。docker可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的linux服务器，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口（类iphone的app），并且容器开销极其低。

## 原则
* 不是虚拟机，而是带独立文件系统、网络的进程（Container not VM）
* 如果在 container 中创建进程，则该进程跑在 host 上
* 使用非 root 用户运行容器（在 Dockerfile 中先创建用户）
* Dockerfile 中尽量减少层，比如 RUN 后的命令进行 && 合并

## 安装
* 使用 docker 官方在线脚本进行安装：curl -sSL https://get.docker.com/ | sh 或 wget -qO- https://get.docker.com/ | sh

docker run
* -u {username} 以指定用户运行容器。为安全起见，一定不要用 root 用户跑容器。
* 在 Dockerfile 中 RUN useradd {username} 建立用户，host 上也建立一个相应用户。
* -v /host:/container 挂载 host 目录到容器中，要挂载多个目录的话 -v 多次。
* -p ip:hostPort:containerPort 将容器使用的端口暴露到 host 端口上，要暴露多个端口的话 -p 多次。

## 体验
* Docker 降低了应用的更新/部署难度，Dockerfile -> Image -> Pull -> Run
* Docker Hub 集成了 GitHub，项目代码提交后自动构建 Image
* 从 Docker Hub 上 pull 略慢，但总的来说效率还是高于手工部署的方式且不易出错

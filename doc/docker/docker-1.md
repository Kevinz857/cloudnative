# Docker架构

&emsp;&emsp; Docker使用了C/S体系架构，Docker客户端与Docker守护进程通信，Docker守护进程负责构建，运行和分发Docker容器。Docker客户端和守护进程可以在同一个系统上运行，也可以将Docker客户端连接到远程Docker守护进程。Docker客户端和守护进程使用REST API通过UNIX套接字或网络接口进行通信。

## Docker engine


Docker engine是一个C/S模式的应用，主要包含以下几个组件

* docker-daemon守护进程
* REST格式的API接口用于和docker daemon进程交互
* 命令行工具

![Nx4QMR.png](https://s1.ax1x.com/2020/07/04/Nx4QMR.png)

&emsp;&emsp; Docker命令行工具本身是通过REST API去控制docker daemon进程并与之交互
&emsp;&emsp; Docker daemon进程创建和管理docker对象，比如镜像、容器、网络和存储卷

&emsp;&emsp; Note: Docker遵循 Apache 2.0 开源协议


## Docker architecture

![Nxh72d.png](https://s1.ax1x.com/2020/07/04/Nxh72d.png)

* Docker Damon DockerD用来监听Docker API的请求和管理Docker对象，比如镜像、容器、网络和Volume

* Docker Client docker client是我们和Docker进行交互的最主要的方式方法，比如可以通过docker run来运行一个容器，然后我们的这个client会把命令发送给上面的Docker

* Docker Registry 用来存储Docker镜像的仓库，Docker Hub是Docker官方提供的一个公共仓库，而且Docker默认也是从Docker Hub上查找镜像的，当然你也可以很方便的运行一个私有仓库，当我们使用docker pull或者docker run命令时，就会从我们配置的Docker镜像仓库中去拉取镜像，使用docker push命令时，会将我们构建的镜像推送到对应的镜像仓库中

* Images 镜像，镜像是一个制度模板，带有Docker容器的说明，一般来说的，镜像会基于另外的一些基础镜像上面安装一个Nginx服务器，这样就可以构建一个属于我们自己的镜像了

* Containers 容器，容器是一个镜像的可运行的实例，可以使用Docker REST API或者CLI来操作容器，容器的实质是进程，但与直接在宿主执行的实例进程不同，容器进程属于自己的独立的命名空间。因此容器可以拥有自己的root文件系统、自己的网络配置、自己的进程空间、甚至自己的用户ID。容器内的经常是运行在一个隔离的环境里，使用起来，就好像在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全

## Docker 核心概念


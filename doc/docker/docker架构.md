# Docker架构

> Docker使用了C/S体系架构，Docker客户端与Docker守护进程通信，Docker守护进程负责构建，运行和分发Docker容器。Docker客户端和守护进程可以在同一个系统上运行，也可以将Docker客户端连接到远程Docker守护进程。Docker客户端和守护进程使用REST API通过UNIX套接字或网络接口进行通信。

## Docker engine


&emsp;&emsp; Docker engine是一个C/S模式的应用，主要包含以下几个组件

* Docker-daemon守护进程
* REST格式的API接口用于和Docker daemon进程交互
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


* Services，通过多个docker daemon进程对容器进程扩容，比如docker-swarm

## Docker 核心概念

### Namespaces
&emsp;&emsp;Linux 内核2.4.19中开始陆续引用了namespace概念。目的是将某个特定的全局系统资源(global system resource)通过抽象方法使得namespace中的进程看起来拥有它们自己的隔离的全局系统资源实例

命名空间是Linux内核强大的特性。每个容器都有自己的命名空间，运行在其中的应用都是在独立操作系统中运行一样。命名空间保证了容器之间彼此互不影响

稍后会将namespace单独拎出来细说


### Control groups
Docker Engine on Linux also relies on another technology called control groups (cgroups). A cgroup limits an application to a specific set of resources. Control groups allow Docker Engine to share available hardware resources to containers and optionally enforce limits and constraints. For example, you can limit the memory available to a specific container.

&emsp;&emsp; Docker容器使用Linux namespace来隔离其运行环境，使得容器中的进程看起来就像在一个独立的环境中运行。但是光有运行环境隔离还不够，因为这些进程还是可以不受限制地使用系统资源，比如网络、磁盘、CPU以及内存等。关于其目的，是为了防止它占用了太多的资源而影响到其它进程；另一方面，在系统资源耗尽的时候，Linux内核会触发OOM (out of memory killer，OOM会在系统内存耗尽的情况下跳出来，选择性的干掉一些进程以求释放一些内存)这会让一些被杀掉的进程成了无辜的替死鬼，因此为了让容器中的进程更加可控，Docker使用Linux cgroups来限制容器中的进程允许使用的系统资源

Linux Cgroup可以让系统中所运行任务(进程)的用户定义组分配资源—比如CPU时间、系统内存、网络带宽


### Union file systems
&emsp;&emsp;Union file systems, or UnionFS 可以把文件系统上多个目录(分支)内容联合挂载到同一个目录下，而目录的物理位置是分开的。 
要理解unionFS，我们首先需要先了解bootfs和rootfs
1. boot file system (bootfs) 包含操作系统boot loader和kernel。用户不会修改这个文件系统，一旦启动成功后，整个Linux内核加载进内存，之后bootfs会被卸载掉，从而释放内存。同样的内核版本不同Linux发行版，其bootfs都是一直的
2. root file system (rootfs) 包含典型的目录结构(/dev/,/proc,/bin,/etc,/lib,/usr,/tmp)

假设Dockerfile内容如下

```shell
FROM ubuntu:14.04
ADD run.sh /
VOLUME /data
CMD ["./run.sh"]
```

联合文件系统对应的层次结构如下图所示



![NxTx3D.png](https://s1.ax1x.com/2020/07/04/NxTx3D.png)


图中的顶上两层，是Docker为Docker容器新建的内容，而这两层属于容器范畴。这两层分别为Docker容器的初始层(init Layer)与可读写层(Read-write Layer)

**初始层**: 大多是初始化容器环境时，与容器相关的环境信息，如容器主机名，主机host信息以及域名服务文件等。

**读写层**: Docker容器内的进程只对可读可写层拥有写权限，其它层进程而言都是只读的(Read-Only)。关于VOLUME以及容器的host、hostname、resolv.conf文件都会挂载到这里

* FROM ubuntu:14.04 设置基础镜像，此时会使用Ubuntu:14.04作为基础镜像
* ADD run.sh / 将Dockerfile所在目录下的run.sh加至镜像的根目录，此时新一层的镜像只有一项内容，即根目录下的run.sh
* VOLUME /data 设置镜像的存储，此VOLUME在容器内部的路径为/data。此时并未在新一层的镜像中添加任何文件，但是更新了镜像的json文件，以便通过此镜像启动容器时获取这方面的信息(下面会有详细的介绍)
* CMD [“./run.sh”] 设置镜像的默认执行入口，此命令同样不会在新建镜像中添加任何文件，仅仅在上一层镜像json文件的基础上更新新的镜像的json文件


### Docker存储驱动

&emsp;&emsp; Docker最开始采用AUFS作为文件系统，也得益于AUFS分层的概念，实现了多个Container可以共享一个image。但是由于AUFS未并入Linux内核，且只支持Ubuntu，考虑到兼容性问题，在Docker 0.7 版本中引入了存储驱动，目前，Docker支持AUFS、Btrfs、Devicemapper、OverlayFS、ZFS、VFS六种存储驱动。



### 原理说明

> 写时复制 (CoW)

&emsp;&emsp; 所有驱动都用到的技术————写时复制，Cow全称copy-on-write，表示只是在需要写时才去复制，这个是针对已有文件的修改场景。比如基于一个image启动多个Container，如果每个Container都去分配一个image一样的文件系统，那么将会占用大量的磁盘空间。而CoW技术可以让所有的容器共享image的文件系统，所有数据都从image中读取，只有当要对文件进行写操作时，才从image里把要写的文件复制到自己的文件系统进行修改。所以无论有多少个容器共享一个image，所做的写操作都是对从image中复制到自己的文件系统的副本上进行，并不会修改image的源文件，且多个容器操作同一个文件，会在每个容器的文件系统里生成一个副本，每个容器修改的都是自己的副本，互相隔离，互不影响。使用CoW可以有效的提高磁盘的利用率。

> 用时分配 （allocate-on-demand）

&emsp;&emsp; 写是分配是用在原本没有这个文件的场景，只有在要新写入一个文件时才分配空间，这样可以提高存储资源的利用率。比如启动一个容器，并不会因为这个容器分配一些磁盘空间，而是当有新文件写入时，才按需分配新空间。



#### 存储驱动介绍

官方文档介绍很详细
* [AUFS](https://docs.docker.com/storage/storagedriver/aufs-driver/)
* [OverlayFS](https://docs.docker.com/storage/storagedriver/overlayfs-driver/)
* [Devicemapper](https://docs.docker.com/storage/storagedriver/device-mapper-driver/)
* [Btrfs](https://docs.docker.com/storage/storagedriver/brtfs-driver/)
* [ZFS](https://docs.docker.com/storage/storagedriver/zfs-driver/)
* [VFS](https://docs.docker.com/storage/storagedriver/vfs-driver/)




## 参考
https://docs.docker.com
http://dockone.io/article/1765

# Docker初探


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

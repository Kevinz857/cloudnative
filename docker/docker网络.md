

> 官方文档说的很详细，直接撸官方文档吧

## Network drivers


&emsp;&emsp; Docker’s networking subsystem is pluggable, using drivers. Several drivers exist by default, and provide core networking functionality:


* bridge: The default network driver. If you don’t specify a driver, this is the type of network you are creating. Bridge networks are usually used when your applications run in standalone containers that need to communicate. [See bridge networks](https://docs.docker.com/network/network-tutorial-standalone/).

* host: For standalone containers, remove network isolation between the container and the Docker host, and use the host’s networking directly. host is only available for swarm services on Docker 17.06 and higher. [See use the host network](https://docs.docker.com/network/network-tutorial-host/).

* overlay: Overlay networks connect multiple Docker daemons together and enable swarm services to communicate with each other. You can also use overlay networks to facilitate communication between a swarm service and a standalone container, or between two standalone containers on different Docker daemons. This strategy removes the need to do OS-level routing between these containers. [See overlay networks](https://docs.docker.com/network/network-tutorial-overlay/).

* macvlan: Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network. The Docker daemon routes traffic to containers by their MAC addresses. Using the macvlan driver is sometimes the best choice when dealing with legacy applications that expect to be directly connected to the physical network, rather than routed through the Docker host’s network stack. [See Macvlan networks](https://docs.docker.com/network/network-tutorial-macvlan/).

* none: For this container, disable all networking. Usually used in conjunction with a custom network driver. none is not available for swarm services. [See disable container networking](https://docs.docker.com/network/none/).

* [Network plugins](https://docs.docker.com/engine/extend/plugins_services/): You can install and use third-party network plugins with Docker. These plugins are available from Docker Hub or from third-party vendors. 

## Network driver summary

* User-defined bridge networks are best when you need multiple containers to communicate on the same Docker host.
Host networks are best when the network stack should not be isolated from the Docker host, but you want other aspects of the container to be isolated.
* Overlay networks are best when you need containers running on different Docker hosts to communicate, or when multiple applications work together using swarm services.
* Macvlan networks are best when you are migrating from a VM setup or need your containers to look like physical hosts on your network, each with a unique MAC address.
* Third-party network plugins allow you to integrate Docker with specialized network stacks.



## 参考
https://docs.docker.com/network/
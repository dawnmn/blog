Docker使用Google公司推出的Go语言进行开发实现，基于Linux内核的cgroup，namespace，以及Union FS等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。
传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

镜像（Image）
是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。每个镜像都由很多层次构成，Docker 使用 Union FS 将这些不同的层结合到一个镜像中去。
容器（Container）
镜像和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器的实质是进程,容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。容器消亡时，容器存储层也随之消亡。因此，所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录。
仓库（Repository）
集中的存储、分发镜像的服务

DockerFile
用于构建镜像，每个指令就是一层。镜像是多层存储，每一层的东西并不会在下一层被删除，会一直跟随着镜像。因此镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。
虽然表面上我们好像是在本机执行各种 docker 功能，但实际上，一切都是使用的远程调用(Docker Remote API)形式在服务端（Docker 引擎）完成。也因为这种 C/S 设计，让我们操作远程服务器的 Docker 引擎变得轻而易举。
指令：FROM RUN COPY CMD ENV ARG VOLUME WORKDIR
docker容器内没有后台服务的概念，容器中的应用都应该以前台执行。
多阶段构建，减小镜像的体积。

数据卷
用来持久化数据的
-v my-vol:/usr/share/nginx/html 等同于 --mount source=my-vol,target=/usr/share/nginx/html
挂载一个主机目录作为数据卷:
-v /var/data/html:/usr/share/nginx/html

容器互联network
docker容器支持导入导出
.dockerignore文件使用方式与.gitignore相似

Docker Compose
单机，用于容器编排，使用docker-compose.yml模板文件。docker compose也是调用docker api
Swarm
Swarm是使用SwarmKit构建的 Docker 引擎内置（原生）的集群管理和编排工具，可以借助Docker Compose管理容器
Kubernetes
管理跨多个主机的容器，提供基本的部署，维护以及应用伸缩，主要实现语言为 Go 语言。









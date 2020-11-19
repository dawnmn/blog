[官网安装](https://docs.docker.com/engine/install/centos/)
配置镜像源：
参见[Docker Hub 源使用帮助](http://mirrors.ustc.edu.cn/help/dockerhub.html)
```
vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}

systemctl restart docker
```
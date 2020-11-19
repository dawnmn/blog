[官网安装](https://docs.docker.com/engine/install/centos/)
配置镜像源：
参见[镜像加速器](https://github.com/yeasy/docker_practice/blob/master/install/mirror.md)
```
vim /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}

systemctl daemon-reload
systemctl restart docker
```
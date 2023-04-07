**windows**
[官网安装](https://docs.docker.com/engine/install/centos/) Docker，启动Docker桌面

**linux**
```
yum install -y yum-utils
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

systemctl start docker
docker run hello-world

#检查服务是否开机启动
systemctl is-enabled docker.service
#将服务配置成开机启动
systemctl enable docker.service
#启动服务
systemctl start docker.service
#关闭开机启动
systemctl disable docker.service
```
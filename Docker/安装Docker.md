#### windows
[官网安装](https://www.docker.com/get-started/) Docker，启动Docker桌面

#### linux
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

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
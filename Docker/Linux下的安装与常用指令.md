**安装**
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

**常用指令**
```
docker rm containerId
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
docker rmi -f $(docker images -qa)
docker ps -a
docker search filebeat
docker exec -it elk /bin/bash
docker logs -f containerId

#导出与导入
docker export 7691a814370e > ubuntu.tar
cat ubuntu.tar | docker import - test/ubuntu:v1.0
docker image ls

#Docker Hub中查询
docker search centos

```
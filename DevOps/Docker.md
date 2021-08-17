#### 安装
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

systemctl start docker
docker run hello-world
```

#### 常用指令
```
docker rm containerId
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
docker rmi -f $(docker images -qa)
docker ps -a
docker search filebeat
docker exec -it elk /bin/bash
```
```
docker pull centos:centos7
docker images

docker run -itd -p 50022:22 --privileged --name myCentos centos:centos7 /usr/sbin/init
docker exec -it myCentos /bin/bash

yum -y install net-tools
yum -y install openssh-server
passwd

systemctl start sshd
```
xshell连接，ip是docker宿主机的局域网ip，端口号是50022，这里与vmware是有区别的。
可以通过[lnmp](https://hub.docker.com/r/2233466866/lnmp)搭建基于centos的lnmp环境
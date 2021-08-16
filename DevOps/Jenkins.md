#### Docker安装
[官网安装](https://www.jenkins.io/doc/book/installing/docker/)

```
# docker images
# netstat -tlunp
# docker stop $(docker ps -aq)
docker ps
docker stop containerId
#按照官网启动镜像

# 浏览器访问 http://192.168.152.132:8080/

# 查看密码
docker exec -it containerId /bin/bash
cat /var/jenkins_home/secrets/initialAdminPassword
exit
```
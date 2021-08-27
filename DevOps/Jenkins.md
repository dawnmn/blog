#### Docker安装
```
docker pull jenkins/jenkins:lts
chown -R 1000:1000 /data/jenkins_home
docker run -d --name jenkins -p 8081:8080 -v /data/jenkins_home:/var/jenkins_home jenkins/jenkins:lts



# 浏览器访问 http://192.168.152.132:8081/

# 查看密码
docker exec -it containerId /bin/bash
cat /var/jenkins_home/secrets/initialAdminPassword
exit
```
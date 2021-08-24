#### 安装
[官网安装](https://about.gitlab.com/install/?version=ce#centos-7)，然后配置url
```
docker pull gitlab/gitlab-ce

mkdir -p /var/gitlab/etc
mkdir -p /var/gitlab/log
mkdir -p /var/gitlab/data

docker run --detach \
--hostname 192.168.152.132 \
--publish 443:443 --publish 80:80 --publish 2222:22 \
--volume /var/gitlab/etc:/etc/gitlab \
--volume /var/gitlab/log:/var/log/gitlab \
--volume /var/gitlab/data:/var/opt/gitlab \
--name gitlab \
--restart always \
--privileged=true \
gitlab/gitlab-ce

docker logs -f gitlab
```
浏览器访问`http://192.168.152.132`

登录账号：`root`
登录密码获取方式如下：
`docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password`
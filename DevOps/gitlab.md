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

#### 配置
```
vim /var/gitlab/etc/gitlab.rb
# 修改ssh clone时显示的端口
gitlab_rails['gitlab_shell_ssh_port'] = 2222

docker stop gitlab
docker rm gitlab
systemctl restart docker
```
重启gitlab

**SSH克隆，配置SSH**
在需要克隆的主机上运行：`ssh-keygen -t rsa -C "标题"`，一路回车，生成文件。点击`头像->Preferences->SSH Keys`，将`/root/.ssh/id_edxxx.pub`内容复制上去。

**HTTP克隆，配置Personal Access Tokens**
点击`头像->settings->Access Tokens`，账号是gitlab账号，密码是生成的token。

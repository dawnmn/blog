**安装**
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

**配置**
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
在需要克隆的主机上运行：`ssh-keygen -t rsa -C "你的标题"`，一路回车，生成文件。点击`头像->Preferences->SSH Keys`，将`/root/.ssh/id_rsa.pub`内容复制上去。

**HTTP克隆，配置Personal Access Tokens**
点击`头像->settings->Access Tokens`，账号是gitlab账号，密码是生成的token。

**PHP自动拉取**
1 配置代码服务器用户
这里www为你的nginx用户名
```
cat /etc/passwd | grep www
vim /etc/passwd
# 将www的shell改成/bin/bash

chmod 777 /etc/sudoers
vi /etc/sudoers
www ALL=(ALL)       ALL
chmod 440 /etc/sudoers

chown -R www:www wwwroot

su www
cd /home/wwwroot
```
按照上面的SSH步骤，配置SSH，SSH克隆，`chown -R www:www *`。注意，路径是：`/home/www/.ssh/`，`cp /home/www/.ssh/* /root/.ssh`

2 配置拉取程序
进入项目的web目录，编辑如下，并配置好nginx，让其能够访问：
```
vim webhook.php

<?php

$path = "/home/nginx/www/ddd-api/";

if (empty($requestBody = file_get_contents("php://input"))) {
    die('send fail');
}
$content = json_decode($requestBody, true);

if ($content['ref']=='refs/heads/test' && $content['total_commits_count']>0) {
    file_put_contents("a.txt", var_export(shell_exec("cd {$path} && git pull origin test 2>&1"), true));
}
```

3 gitlab配置
网页配置允许局域网钩子：`Admin->Settings->Network->Outbound requests` 勾选。
网页配置webhooks：`Settings->Webhooks`
URL：`http://192.168.152.128:9901/webhook.php` （你的webhook脚本路径）
Secret token：123123 （随便填，用处是在php验证是gitlab发出的）


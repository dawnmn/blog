#### 安装
[官网安装](https://about.gitlab.com/install/?version=ce#centos-7)，然后配置url
```
vim /etc/gitlab/gitlab.rb
external_url 'http://192.168.152.132'

gitlab-ctl reconfigure
gitlab-ctl restart
gitlab-ctl status
```
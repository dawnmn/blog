**安装**
参考[官网](http://supervisord.org/installing.html)：
```
pip install supervisor

echo_supervisord_conf > /etc/supervisord.conf
mkdir /etc/supervisord.d

vim /etc/supervisord.conf
# inet_http_server 配置是否http访问
[include]
files = /etc/supervisord.d/*.conf
```
**配置被监控的应用**
```
cd /etc/supervisord.d
vim hello.conf

[program:hello]
command=/usr/local/php/bin/php /var/download/hello.php
directory=/var/download
autostart=true
autorestart=true
stderr_logfile=/var/download/hello.log
stdout_logfile=/var/download/hello.log
```
**启动和控制**
```
# 启动
supervisord -c /etc/supervisord.conf
# 控制
supervisorctl status
supervisorctl stop xxx_app
supervisorctl start xxx_app

# 修改配置，只重启被修改配置的应用
supervisorctl reread
supervisorctl update
```
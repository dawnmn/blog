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
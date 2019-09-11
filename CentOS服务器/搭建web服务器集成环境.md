#### **Nginx**
[官网安装](http://nginx.org/en/linux_packages.html#RHEL-CentOS)
允许外网访问
```
systemctl stop firewalld.service
systemctl disable firewalld.service
service iptables stop
chkconfig iptables off

vim  /etc/selinux/config
SELINUX=disabled
```
重启服务器

```
service nginx start
```

#### **MySQL5.7**
[参见MySQL](https://www.kancloud.cn/book/dawnmn/blog/preview/CentOS%E6%9C%8D%E5%8A%A1%E5%99%A8/MySQL5.7/%E5%AE%89%E8%A3%85mysql5.7.md)

#### **PHP7.1**
[官网下载](http://php.net/downloads.php)php7.1源码包，解压并进入目录
安装
```
yum groupinstall -y Development tools
yum install libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel m4 autoconf
 
./configure --prefix=/usr/local/php --with-curl --with-freetype-dir --with-gd --with-gettext --with-iconv-dir --with-kerberos --with-libdir=lib64 --with-libxml-dir --with-mysqli --with-openssl --with-pcre-regex --with-pdo-mysql --with-pdo-sqlite --with-pear --with-png-dir --with-jpeg-dir --with-xmlrpc --with-xsl --with-zlib --with-bz2 --with-mhash --enable-fpm --enable-bcmath --enable-libxml --enable-inline-optimization --enable-gd-native-ttf --enable-mbregex --enable-mbstring --enable-opcache --enable-pcntl --enable-shmop --enable-soap --enable-sockets --enable-sysvsem --enable-sysvshm --enable-xml --enable-zip
make && make install
```
配置环境变量
```
vim /etc/profile
PATH=$PATH:/usr/local/php/bin
export PATH
 
source /etc/profile
 
php -v
```
配置php-fpm
```
cp php.ini-production /usr/local/php/lib/php.ini
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
 
vim /usr/local/php/etc/php-fpm.d/www.conf
user = nginx
group = nginx
 
service php-fpm start
```

#### **Redis**
[官网下载](https://redis.io/download)，解压并进入目录
```
make PREFIX=/usr/local/redis install
cp redis.conf  /usr/local/redis/bin
```
[pecl下载]([https://pecl.php.net/package/redis](https://pecl.php.net/package/redis))PHP扩展并解压，进入目录
```
phpize
./configure
make && make install

vim /usr/local/php/lib/php.ini
extension=redis.so
```

#### **Supervisor**
[官方文档](http://supervisord.org/)
安装
```
yum install epel-release

yum install python-pip
 
pip install --upgrade pip
 
pip install supervisor
 
mkdir -p /etc/supervisor/conf.d
echo_supervisord_conf > /etc/supervisor/supervisord.conf
```
```
[inet_http_server]         ; inet (TCP) server disabled by default
port=*:9001        ; ip_address:port specifier, *:port for all iface
username=user              ; default is no username (open server)
password=123               ; default is no password (open server)
 
[supervisord]
...
minfds=65535
 
[include]
files = /etc/supervisor/conf.d/*.conf
```
设置文件描述符数目
```
ulimit -n
 
vim /etc/security/limits.conf
* soft nofile 65535
* hard nofile 65535

ulimit -a
ulimit -n
```
开机自启
```
vim  /usr/lib/systemd/system/supervisord.service
```
```
#supervisord.service
 
[Unit] 
Description=Supervisor daemon
 
[Service] 
Type=forking 
ExecStart=/usr/bin/supervisord -c /etc/supervisor/supervisord.conf 
ExecStop=/usr/bin/supervisorctl shutdown 
ExecReload=/usr/bin/supervisorctl reload 
KillMode=process 
Restart=on-failure 
RestartSec=42s
 
[Install] 
WantedBy=multi-user.target
```
```
systemctl enable supervisord
```
配置被管理的应用
```
cd /etc/supervisor/conf.d
```
先关闭各应用的守护进程模式，再配置
nginx
```
vim nginx.conf

[program:nginx]
command = /usr/sbin/nginx -g 'daemon off;'
user = root
autostart = true
autoresart = true
stderr_logfile = /var/log/supervisor/nginx.stderr.log
```
php-fpm
```
vim php -fpm.conf

[program:php-fpm]
command = bash -c "sleep 1 && /usr/local/php/sbin/php-fpm --fpm-config /usr/local/php/etc/php-fpm.conf --pid /usr/local/php/var/run/php-fpm.pid"
user = root
autostart = true
autoresart = true
startretries=5
exitcodes=0,2,70
stopsignal=QUIT
stopwaitsecs=2
stderr_logfile = /var/log/supervisor/php-fpm.stderr.log
```
redis
```
vim redis.conf

[program:redis]
command = /usr/local/redis/bin/redis-server
user = root
autostart = true
autoresart = true
stderr_logfile = /var/log/supervisor/redis.stderr.log
```
php脚本
```
vim php-task.conf

[program:php-task]
command = /usr/local/php/bin/php /var/path/php-task.php
user = root
autostart = true
autoresart = true
stderr_logfile = /var/log/supervisor/php-task.stderr.log
```









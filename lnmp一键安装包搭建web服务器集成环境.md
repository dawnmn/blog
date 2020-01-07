#### **lnmp1.6**
[官网安装](https://lnmp.org/install.html)
```
yum install screen
screen -S lnmp

wget http://soft.vpser.net/lnmp/lnmp1.6.tar.gz -cO lnmp1.6.tar.gz && tar zxf lnmp1.6.tar.gz && cd lnmp1.6 && ./install.sh lnmp
#大约40分钟左右的安装时间
```
惯例配置
```
# 配置phpmyadmin
vim /home/wwwroot/default/phpmyadmin/config.inc.php
$cfg['Servers'][$i]['auth_type'] = 'config'; // 修改为config
...
$cfg['Servers'][$i]['uer'] = 'root'; // 新增
$cfg['Servers'][$i]['password'] = 'Jsdf234+'; // 新增
```

配置路径
```
/usr/local/php/etc/php.ini

/usr/local/nginx/conf/nginx.conf
/usr/local/nginx/conf/vhost/xx.conf
/home/wwwroot

/etc/my.cnf
```
指令
```
lnmp restart
```
nginx支持pathinfo
```
#配置fastcgi
vim /usr/local/nginx/conf/fastcgi.conf
fastcgi_param PHP_ADMIN_VALUE"open_basedir=$document_root/:/tmp/:/proc/";
fastcgi_param PHP_ADMIN_VALUE $basedir if_not_empty;

#复制/usr/local/nginx/conf/nginx.conf里面的default配置，修改如下
vim /usr/local/nginx/conf/vhost/xxx.com.conf
server
{
	...

	set $basedir "open_basedir=/home/wwwroot/project_name/:/tmp/:/proc/";

	include enable-php-pathinfo.conf;

	location / {
		try_files $uri $uri/ /index.php$is_args$args;
	}
	...
}

```
切换PHP版本
```
cd lnmp1.6
./install.sh mphp

vim /usr/local/nginx/conf/vhost/xxx.conf
include enable-php7.2.conf;
```
#### **redis**
[官网下载](https://redis.io/download)，解压并进入目录
```
make PREFIX=/usr/local/redis install

mkdir /etc/redis

cp redis.conf /etc/redis/6379.conf
vim /etc/redis/6379.conf
daemonize yes

cp utils/redis_init_script /etc/init.d/redisd
vim /etc/init.d/redisd
EXEC=/usr/local/redis/bin/redis-server
CLIEXEC=/usr/local/redis/bin/redis-cli

#打开服务
service redisd start
#关闭服务
service redisd stop
#设置为开机自启动服务器
chkconfig redisd on

#测试
/usr/local/redis/bin/redis-cli
ping
#出现PONG表示成功
```
[pecl下载](https://pecl.php.net/package/redis)PHP扩展并解压，进入目录
```
phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install

vim /usr/local/php/etc/php.ini
extension=redis.so
```
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
$cfg['Servers'][$i]['password'] = 'yourpassword'; // 新增
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
```
nginx配置新的服务
```
#复制/usr/local/nginx/conf/nginx.conf里面的default配置，修改如下
vim /usr/local/nginx/conf/vhost/xxx.com.conf
新增
set $basedir "open_basedir=/home/wwwroot/project_name/:/tmp/:/proc/";

include enable-php-pathinfo.conf;

location / {
	try_files $uri $uri/ /index.php$is_args$args;
}
最终结果类似如下
server
{
	listen 80;
	server_name www.yourdomain.com;
	index index.html index.htm index.php;
	root  /home/wwwroot/yourproject;

	set $basedir "open_basedir=/home/wwwroot/yourproject/:/tmp/:/proc/";
	include enable-php-pathinfo.conf;
	include enable-php.conf;

	location / {
		try_files $uri $uri/ /index.php$is_args$args;
	}

	location ~ /\.
	{
		deny all;
	}

	access_log  /home/wwwlogs/access.log;
}
```
切换PHP版本
```
cd lnmp1.6
./install.sh mphp

vim /usr/local/nginx/conf/vhost/xxx.conf
include enable-php7.2.conf;
```

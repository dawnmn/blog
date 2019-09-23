#### **lnmp**
[官网安装]([https://lnmp.org/install.html](https://lnmp.org/install.html))
```
yum install screen
screen -S lnmp

wget http://soft.vpser.net/lnmp/lnmp1.6.tar.gz -cO lnmp1.6.tar.gz && tar zxf lnmp1.6.tar.gz && cd lnmp1.6 && ./install.sh lnmp
#大约40分钟左右的安装时间
```
配置路径
```
/usr/local/php/etc/php.ini

/usr/local/nginx/conf/nginx.conf
/usr/local/nginx/conf/vhost/xx.conf
/home/wwwroot

/etc/my.cnf
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
```
[pecl下载](https://pecl.php.net/package/redis)PHP扩展并解压，进入目录
```
phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install

vim /usr/local/php/etc/php.ini
extension=redis.so
```
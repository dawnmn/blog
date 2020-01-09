[官网下载](https://redis.io/download)，解压并进入目录
```
make PREFIX=/usr/local/redis install

mkdir /etc/redis

cp redis.conf /etc/redis/6379.conf
vim /etc/redis/6379.conf
daemonize yes
#bind 0.0.0.0  # 设置外部连接
#requirepass yourpassword # 设置密码

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
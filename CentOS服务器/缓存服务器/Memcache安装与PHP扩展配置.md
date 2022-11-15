**安装**
```
yum install memcached
```
**启动**
```
/usr/bin/memcached -d -l 127.0.0.1 -p 11211 -m 32 -u root # 分配32M内存
ps -ef | grep memcached

#开机启动
vim /etc/rc.d/rc.local
/usr/bin/memcached -d -l 127.0.0.1 -p 11211 -m 32 -u root
```
**libmemcached安装**
```
官网下载最新版本，解压，进入目录

./configure --prefix=/usr/lib/libmemcached
make && make install
```
**memcached的PHP扩展安装**
```
官网下载最新版本，解压，进入目录

phpize
./configure --with-libmemcached-dir=/usr/lib/libmemcached
make && make install

vim /usr/local/php/lib/php.ini
extension=memcached.so

phpinfo()查看是否成功
```
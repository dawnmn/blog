#### **lnmp1.6**
[官网安装](https://lnmp.org/install.html)
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
nginx支持thinkphp5
```
vim /usr/local/php/etc/php.ini
display_errors = On

vim /usr/local/nginx/conf/fastcgi.conf
fastcgi_param PHP_ADMIN_VALUE"open_basedir=$document_root/:/tmp/:/proc/";
fastcgi_param PHP_ADMIN_VALUE $basedir if_not_empty;


vim /usr/local/nginx/conf/vhost/xxx.com.conf
server
    {
        listen 80;
        server_name api.sofa.com;
        index index.html index.htm index.php;
        root  /home/wwwroot/sofa/api/public;

        set $basedir "open_basedir=/home/wwwroot/sofa/api/:/tmp/:/proc/";

        location / {
                index  index.php index.html index.htm;
                #如果请求既不是一个文件，也不是一个目录，则执行一下重写规则
                if (!-e $request_filename)
                {
                        #地址作为将参数rewrite到index.php上。
                        rewrite ^/(.*)$ /index.php/$1;
                        #若是子目录则使用下面这句，将subdir改成目录名称即可。
                        #rewrite ^/subdir/(.*)$ /subdir/index.php/$1;
                }
        }

        include enable-php-pathinfo.conf;

        location /nginx_status
        {
            stub_status on;
            access_log   off;
        }

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /.well-known {
            allow all;
        }

        location ~ /\.
        {
            deny all;
        }

        access_log  /home/wwwlogs/access.log;
    }

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
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install

vim /usr/local/php/etc/php.ini
extension=redis.so
```
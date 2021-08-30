**安装nginx**
```
docker pull nginx
# 检查是否成功
docker run --name nginx -p 9601:80 -d nginx
# 访问 http://192.168.152.134:9601
docker stop nginx
docker rm nginx
```
**安装php**
```
docker pull php:7.3.30-fpm
docker run -d -v /var/www:/var/www/html -p 9000:9000 --name phpfpm73 php:7.3.30-fpm
# 检查是否成功
netstat -tlunp|grep 9000
```
**配置nginx连接php**
```
mkdir /etc/nginx/conf/conf.d
vim /etc/nginx/conf/conf.d/phpinfo.conf
server {
    listen       80;
    server_name  192.168.152.134;

    location / {
        root   /usr/share/nginx/html;
        index  index.php  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html/$fastcgi_script_name;
        include        fastcgi_params;
    }
}

# 启动nginx
docker run -d -p 9601:80 \
-v /var/www:/usr/share/nginx/html:ro \
-v /etc/nginx/conf/conf.d:/etc/nginx/conf.d:ro \
--link phpfpm73:php \
--name nginx nginx

vim /var/www/phpinfo.php
<?php
phpinfo();

# 访问 http://192.168.152.134:9601/phpinfo.php

```
**安装php扩展**
以redis扩展为例，[pecl下载](http://pecl.php.net/package/redis)，并解压
```
docker exec -it phpfpm73 /bin/bash
mkdir -p /usr/src/php/ext
exit

# 将redis安装包复制进php容器
docker cp ./redis /usr/src/php/ext
docker exec -it phpfpm73 /bin/bash
# 查看redis安装包是否成功复制进php容器
cd /usr/src/php/ext/redis
docker-php-ext-install redis

php --ri redis
```

**运行php脚本**
```
cd /var/www
vim a.php
<?php
echo "success\n";

docker exec `docker ps -a | grep '9000/tcp' |awk '{print $1}'` /usr/local/bin/php /var/www/html/a.php
```

**redis**
```
mkdir -p /usr/local/redis/data/

docker run -d \
--privileged=true \
-p 6379:6379 \
-v /usr/local/redis/data/:/data \
--name redis \
redis redis-server \
--appendonly yes \
--requirepass "123456"

docker exec -it redis redis-cli -a 123456
```
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
    listen       9510;
    server_name  192.168.2.64;
	index index.html index.htm index.php;
	root  /var/www/html/test;

	if ($request_method = OPTIONS){
		return 200;
	}

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

	location ~ /\. {
        deny all;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;

        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        try_files $fastcgi_script_name =404;
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
file_put_contents('/var/www/html/a.log', 'success');

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
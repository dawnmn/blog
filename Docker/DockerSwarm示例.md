**1 创建Swarm集群**
准备3个docker主机`192.168.126.129` `192.168.126.128` `192.168.126.1`
```
# 129
docker swarm init --advertise-addr 192.168.126.129

# 128 和 1
docker swarm join --token SWMTKN-1-31kvr0ffntd0m22sx0srnpqcg1o8ctrmxftq9gyj6b2q7x3be7-8n5xvsy31jhcw0g2yi6o4blur 192.168.126.129:2377
```

**2 运行前准备**
129 主机
```
cd yourpath/lnmp
vim docker-compose.yml
```
```
version: "3"

services:
  nginx:
    image: nginx:stable
    ports:
      - "80:80"
      - "9610:9610"
    volumes:
      - nginx-html:/var/www/html
      - nginx-conf:/etc/nginx/conf.d
      - nginx-log:/var/log/nginx
    deploy:
      mode: replicated
      replicas: 3

  php:
    image: dawnmn/php73
    volumes:
      - nginx-html:/var/www/html
    deploy:
      mode: replicated
      replicas: 3

  mysql:
    image: mysql:5.7
    ports:
      - "3306:3306"
    volumes:
      - mysql-db:/var/lib/mysql
      - mysql-conf:/etc/mysql/mysql.conf.d
      - mysql-log:/var/log
    environment:
      MYSQL_ROOT_PASSWORD: "mypass"
    deploy:
      placement:
        constraints: [node.role == manager]

  redis:
    image: redis:7.0
    ports:
      - "6379:6379"
    volumes:
      - redis-db:/data
    deploy:
      placement:
        constraints: [ node.role == manager ]

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - 18080:8080
    stop_grace_period: 1m30s
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    deploy:
      placement:
        constraints: [ node.role == manager ]

volumes:
  nginx-html:
  nginx-conf:
  nginx-log:
  mysql-db:
  mysql-conf:
  mysql-log:
  redis-db:
```

**3 运行**
```
docker stack deploy -c docker-compose.yml new-lnmp

cd /var/lib/docker/volumes/new-lnmp_nginx-conf/_data
vim phpinfo.conf
```
```
server {
    listen       9610;
    server_name  localhost;
	index index.html index.htm index.php;
	root  /var/www/html/hello;

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
```
```
cd /var/lib/docker/volumes/new-lnmp_nginx-html/_data
mkdir hello
cd hello
vim phpinfo.php
```
```
<?php
phpinfo();
```
```
docker service update new-lnmp_nginx
```
**4 访问**
`http://192.168.126.129:18080/`
`http://192.168.126.129:9610/phpinfo.php`
`http://192.168.126.128:9610/phpinfo.php`
`http://192.168.126.1:9610/phpinfo.php`
**集中配置文件示例**
```
# 进入包含index.html、site.conf的目录
cd /yourpath
# 创建配置
docker config create homepage index.html
docker config create nginx_config site.conf
# 应用配置
docker service create \
     --name nginx \
     --config source=nginx_config,target=/etc/nginx/conf.d/site.conf \
     --config source=homepage,target=/usr/share/nginx/html/index.html \
     --publish published=3000,target=80 \
     nginx
```
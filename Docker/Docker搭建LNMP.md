构建PHP镜像，Dockerfile，`docker build -t php73 .`
```
FROM php:7.3.33-fpm

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" && \
    sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list && \
    apt-get update && apt-get install -y unzip git wget libfreetype6-dev libjpeg62-turbo-dev libpng-dev zlib1g-dev libzip-dev imagemagick libmagickwand-dev && \
    tar -xf /usr/src/php.tar.xz && \
    mv php-7.3.33 /usr/src/php && \
    docker-php-ext-install gd bcmath pdo_mysql zip && \
    wget http://pecl.php.net/get/redis-5.3.6.tgz && \
    wget https://pecl.php.net/get/rdkafka-6.0.0.tgz && \
    wget http://pecl.php.net/get/imagick-3.7.0.tgz && \
    wget https://codeload.github.com/edenhill/librdkafka/zip/refs/tags/v1.9.2 && \
    tar -zxvf redis-5.3.6.tgz && \
    tar -zxvf rdkafka-6.0.0.tgz && \
    tar -zxvf imagick-3.7.0.tgz && \
    unzip v1.9.2 && \
    mv redis-5.3.6 /usr/src/php/ext/redis && \
    mv rdkafka-6.0.0 /usr/src/php/ext/rdkafka && \
    mv imagick-3.7.0 /usr/src/php/ext/imagick && \
    cd librdkafka-1.9.2/ && ./configure && make && make install && cd .. && \
    docker-php-ext-install imagick redis rdkafka && \
    rm -rf /usr/src/php && \
    rm -rf *.tgz  && \
    rm -rf *.tar.gz  && \
    rm -rf librdkafka-1.9.2  && \
    php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');" && \
    mv composer.phar /usr/local/bin/composer && \
    composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ && \
    apt-get --purge remove -y unzip git wget && apt-get clean && apt-get autoclean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
```

docker-compose.yml
```
version: "1"

services:
  nginx:
    image: nginx:stable
    container_name: nginx
    restart: always
    ports:
    - "9501:9501"
    - "9502:9502"
    - "8080:8080"
    volumes:
    - /var/log/nginx:/var/log/nginx
    - /etc/nginx/conf.d:/etc/nginx/conf.d
    - /var/www/html:/var/www/html
    networks:
      lnmp:

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    ports:
    - "3306:3306"
    volumes:
    - /etc/mysql:/etc/mysql/mysql.conf.d
    - /var/lib/mysql:/var/lib/mysql
    - /var/log/mysql:/var/log
    environment:
      MYSQL_ROOT_PASSWORD: "mypass"
    networks:
      lnmp:

  redis:
    image: redis:7.0
    container_name: redis
    restart: always
    ports:
    - "6379:6379"
    volumes:
    - /usr/local/redis/data/:/data
    networks:
      lnmp:

  php:
    image: php73
    container_name: php
    restart: always
    volumes:
      - /var/www/html:/var/www/html
    networks:
      lnmp:

networks:
  lnmp:
    driver: bridge
```
准备
```
mkdir -p /etc/nginx/conf.d
vim /etc/nginx/conf.d/default.conf
# 内容为:
server {
    listen       80;
    server_name  127.0.0.1;
}
```
启动 `docker compose up`


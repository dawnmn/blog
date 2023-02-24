```
FROM php:8.2.3-fpm

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" && \
    sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list && \
    apt-get update && apt-get install -y --no-install-recommends zlib1g-dev libpng-dev libmagickwand-dev && \
    curl -O http://pecl.php.net/get/redis-5.3.7.tgz && \
    curl -O http://pecl.php.net/get/imagick-3.7.0.tgz && \
    tar -zxvf redis-5.3.7.tgz && \
    tar -zxvf imagick-3.7.0.tgz && \
    mkdir -p /usr/src/php/ext && \
    mv redis-5.3.7 /usr/src/php/ext/redis && \
    mv imagick-3.7.0 /usr/src/php/ext/imagick && \
    docker-php-ext-install imagick gd bcmath pdo_mysql redis && \
    php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');" && \
    mv composer.phar /usr/local/bin/composer && \
    composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ && \
    apt-get clean && apt-get autoclean && rm -rf /usr/src/php && rm -rf *.tgz && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
```

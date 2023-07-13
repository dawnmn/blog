**安装xdebug**
进入[官网](http://xdebug.org/wizard)

将phpinfo复制粘贴过去，用于匹配合适的xdebug版本：
```
cd /var/download/temp
php -i >> phpinfo.txt
```
下载并安装xdebug
```
wget https://xdebug.org/files/xdebug-3.2.1.tgz
tar -xvzf xdebug-3.2.1.tgz
phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install

vim /usr/local/php/etc/php.ini
#在末尾添加
[xdebug]
zend_extension = xdebug.so

php --ri xdebug
```

**分析脚本**
```
php --ri xdebug

php -d xdebug.mode=profile -d xdebug.output_dir=. /var/data/php-script/hello.php

php -d xdebug.mode=profile -d xdebug.output_dir=. /var/data/php-script/console/yii wireless/stat
```
会生成类似`cachegrind.out.13096`的文件，用phpstorm查看：`Tools`->`analyze xdebug profiler snapshot`

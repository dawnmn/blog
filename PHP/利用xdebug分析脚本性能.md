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
```

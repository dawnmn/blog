#### **lnmp**
[官网安装]([https://lnmp.org/install.html](https://lnmp.org/install.html))
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

/etc/my.cnf
```
#### **redis**

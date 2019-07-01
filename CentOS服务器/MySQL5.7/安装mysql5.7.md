```
# 安装
mkdir /var/download
cd /var/download

wget https://repo.mysql.com/yum/mysql-5.7-community/el/7/x86_64/mysql57-community-release-el7-10.noarch.rpm
yum localinstall mysql57-community-release-el7-10.noarch.rpm
yum install mysql-community-server

service mysqld start
service mysqld status

# 初始化密码
grep 'temporary password' /var/log/mysqld.log
mysql  -uroot  -p
ALTER  USER  'root'@'localhost'  IDENTIFIED  BY  'your-password';

# 允许远程访问
use mysql;
update user set host = '%' where user = 'root' ;
flush privileges;
```
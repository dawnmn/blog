#### docker 安装
参照[docker hub](https://hub.docker.com/_/rabbitmq)
```
docker pull rabbitmq:3.9-management

docker run -p 15672:15672 -p 5672:5672 -d --hostname my-rabbit --name some-rabbit rabbitmq:3.9-management
```
浏览器访问
http://192.168.126.128:15672/
#### 文件安装rabbitmq-server
参照[官网](https://www.rabbitmq.com/install-rpm.html)
```
cd /var/download
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
yum install rabbitmq-server
```
```
#启动rabbitmq
service rabbitmq-server start
service rabbitmq-server status

#安装web界面
rabbitmqctl start_app
rabbitmq-plugins enable rabbitmq_management
rabbitmqctl stop
```
找到rabbit.app这个文件，将`{loopback_users, [<<"guest">>]}`改为`{loopback_users, []}`
```
service rabbitmq-server restart
```
访问`http://ip:15672`，账号：`guest`密码：`guest`
#### PHP示例
[参见github](https://github.com/php-amqplib/php-amqplib)，demo
```
composer require php-amqplib/php-amqplib
```

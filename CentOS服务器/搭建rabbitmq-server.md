#### 安装rabbitmq-server
参照[官网](https://www.rabbitmq.com/documentation.html)
```
cd /var/download
```
更新erlang，[下载合适版本](https://bintray.com/rabbitmq-erlang/rpm/erlang)
```
yum install xxx.rpm

curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
yum install rabbitmq-server
```

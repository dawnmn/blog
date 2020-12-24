#### **安装kafka**
[官网下载](http://kafka.apache.org/downloads)
解压，移动到/usr/local，重命名kafka，注意需要java环境。

```
java -version

#启动
cd /usr/local/kafka
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
bin/kafka-server-start.sh -daemon config/server.properties
```

#### **kafka集群**
通过3台服务器192.168.1.1、192.168.1.2、192.168.1.3部署集群。
先在192.168.1.1上安装kafka，进入目录
```
vim config/server.properties

broker.id=201

delete.topic.enable=true

log.dirs=/usr/local/kafka_2.11-0.11.0.0/logs

zookeeper.connect=192.168.1.1:2181,192.168.1.2:2181,192.168.1.3:2181
```
分别拷贝kafka文件到192.168.1.2、192.168.1.3，并将broker.id设置为202、203即可


#### **配置php扩展**
1 安装librdkafka
[github下载librdkafka](https://github.com/edenhill/librdkafka/releases)
解压进入目录
```
./configure
make && make install
```

2 安装php-rdkafka
[github下载php-rdkafka](https://github.com/arnaud-lb/php-rdkafka/releases)
解压进入目录
```
phpize
./configure
make && make install

配置php.ini
extension=rdkafka.so
```
#### **测试**


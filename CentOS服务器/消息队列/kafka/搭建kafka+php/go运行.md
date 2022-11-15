#### **docker安装kafka**
用bitnami/kafka镜像安装
```
version: "2"
  zookeeper:
    image: docker.io/bitnami/zookeeper:3.8
    user: root
    container_name: zookeeper
    restart: always
    ports:
      - "2181:2181"
    volumes:
      - /var/data/zookeeper_data:/bitnami
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes
    networks:
      - job

  kafka:
    image: docker.io/bitnami/kafka:3.3
    user: root
    container_name: kafka
    restart: always
    ports:
      - "9092:9092"
    volumes:
      - /var/data/kafka_data:/bitnami
    environment:
      - KAFKA_BROKER_ID=1
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://192.168.126.128:9092
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
      - ALLOW_PLAINTEXT_LISTENER=yes
    depends_on:
      - zookeeper
    networks:
      - job

networks:
  job:
```

#### **文件安装kafka**
[官网下载](http://kafka.apache.org/downloads) 选择Binary downloads
解压，移动到/usr/local，重命名kafka，注意需要java环境。

```
java -version

#启动
cd /usr/local/kafka
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
bin/kafka-server-start.sh -daemon config/server.properties
jps
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
分别拷贝/usr/local/kafka_2.11-0.11.0.0文件到192.168.1.2、192.168.1.3，并将broker.id设置为202、203即可


#### **配置php/go扩展**
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

3 confluent-kafka-go
windows需要安装 [TDM](https://jmeubank.github.io/tdm-gcc/articles/2021-05/10.3.0-release)
在linux下编译时不用管cgo
```
go get github.com/confluentinc/confluent-kafka-go/kafka
```
#### **php示例**
[参见官方文档](https://arnaud.le-blanc.net/php-rdkafka-doc/phpdoc/rdkafka.examples.html)


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


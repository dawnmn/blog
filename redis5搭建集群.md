服务器A `192.168.37.132`
```
cd /etc/redis
#复制三份配置文件，并依次配置
vim 7000.conf
vim 7001.conf
vim 7002.conf

#配置项
masterauth yourpassword
requirepass yourpassword
port 7000                                       
bind 192.168.37.132                                       
daemonize yes                              
appendonly yes                          
pidfile /var/run/redis_7000.pid          
cluster-enabled yes                           
cluster-config-file nodes_7000.conf
cluster-node-timeout 15000 

在该目录删除xxx.aof、xxx.rdb、nodes-xxx.conf

#启动
/usr/local/redis/bin/redis-server /etc/redis/7000.conf
/usr/local/redis/bin/redis-server /etc/redis/7001.conf
/usr/local/redis/bin/redis-server /etc/redis/7002.conf

#检测
ps -ef | grep redis
netstat -tnlp | grep redis
```
服务器B `192.168.37.133`
```
cd /etc/redis
#复制三份配置文件，并依次配置
vim 7003.conf
vim 7004.conf
vim 7005.conf

余下步骤与服务器A相同
```
[官网安装ruby](https://www.ruby-lang.org/zh_cn/documentation/installation/)，选择源码编译安装最新版本
```
ruby -v
```
服务器A运行redis-cli创建集群
```
#创建
/usr/local/redis/bin/redis-cli -h 192.168.37.132 -p 7000 -a yourpassword --cluster create 192.168.37.133:7003   192.168.37.133:7004   192.168.37.133:7005  192.168.37.132:7000  192.168.37.132:7001  192.168.37.132:7002 --cluster-replicas 1

#测试 服务器A
/usr/local/redis/bin/redis-cli -h 192.168.37.132 -p 7000 -a yourpassword -c
set test_cluster 100
#测试 服务器B
/usr/local/redis/bin/redis-cli -h 192.168.37.133 -p 7003 -a yourpassword -c
get test_cluster 
```
PHP调用集群
```
$redisCluster = new \RedisCluster(null,['192.168.37.132:7000'], 1.5, 1.5, true, "yourpassword");
echo $redisCluster->get('test_cluster');
```



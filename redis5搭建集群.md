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


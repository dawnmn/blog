#### **实现锁机制**
加锁
```
SET resource-name token-string NX EX max-lock-time
```
释放锁：先判断 token-string，再DEL

#### **发布订阅**
publish.php 发布消息脚本
```
$redis = new \Redis();
$redis->connect('192.168.37.132', 6379);
$redis->auth('yourpassword');
$redis->publish('mytopic', 'asdf');
```
subscribe.php 订阅脚本，该脚本会一直阻塞，等待消息执行
```
ini_set('default_socket_timeout', -1);
$redis = new \Redis();
$redis->pconnect('192.168.37.132', 6379);
$redis->auth('yourpassword');
$redis->subscribe(['mytopic'], function($redis, $channel, $message){
    $redis->connect('192.168.37.132', 6379);
    $redis->auth('yourpassword');
    $redis->set('aa', 300);
    echo $redis->get('aa');
    echo "2 $channel:$message";
});
```
#### **调优**
```
sysctl vm.overcommit_memory=1
vim /etc/sysctl.conf
vm.overcommit_memory = 1
```
#### **从文件中批量插入数据**
1 安装EPEL(Extra Packages for Enterprise Linux )即企业版Linux的扩展包
[下载和安装说明](https://pkgs.org/download/epel-release)
2 安装unix2dos
```
yum install unix2dos
```
3 插入数据
```
vim redis.txt
set myk12 v1
zadd zset12 0 a 1 b 3 c
sadd sset12 e f g hh
set myk22 v2
hset myset12 k1 v1
hmset myset22 k2 v2 k3 v3 k4 v4
set myk32 v3

unix2dos redis.txt
cat redis.txt|/usr/local/redis/bin/redis-cli --pipe
```
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

```
yum install unix2dos

vim redis.txt #不能有多余的制表符
set myk12 v1
zadd zset12 0 a 1 b 3 c
sadd sset12 e f g hh
set myk22 v2
hset myset12 k1 v1
hmset myset22 k2 v2 k3 v3 k4 v4
set myk32 v3

unix2dos redis.txt #必须转码
cat redis.txt|/usr/local/redis/bin/redis-cli --pipe
```

#### **使用管道**
批处理，提高速度。适用场景：当Redis的多次操作毫不相关时，即后一个指令不依赖于上一个指令的执行结果时。
```
$redis = new \Redis();
$redis->connect('192.168.37.132', 6379);
$redis->auth('yourpassword');
$pipe = $redis->multi(\Redis::PIPELINE);
for ($i=0;$i<5;$i++){
    $pipe->incr('pipe');
}
$result = $pipe->exec();
echo $redis->get('pipe');
```

#### **异常**
```
#ERROR
/var/run/redis_6379.pid exists, process is already running or crashed
#SOLUTION
rm -rf /var/run/redis_6379.pid
```
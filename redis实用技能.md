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
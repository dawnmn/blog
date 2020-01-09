#### **实现锁机制**
加锁
```
SET resource-name token-string NX EX max-lock-time
```
释放锁：先判断 token-string，再DEL

#### **发布订阅**
publish.php
```
$redis = new \Redis();
$redis->connect('192.168.37.132', 6379);
$redis->auth('yourpassword');
$redis->publish('mytopic', 'asdf');
```
subscribe.php
```
ini_set('default_socket_timeout', -1);
$redis = new \Redis();
$redis->pconnect('192.168.37.132', 6379);
$redis->auth('yourpassword');
$redis->subscribe(['mytopic'], function($instance, $channel, $message){
    echo "1 $channel:$message";
});
```
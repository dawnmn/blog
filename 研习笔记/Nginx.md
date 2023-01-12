Nginx是由c编写的。

理论上，Nginx的并发上限取决于内存大小。
热部署：master管理进程与worker工作进程的分离设计，不停服升级Nginx的可执行文件、更新配置项、更换日志文件等功能。
修改/etc/sysctl.conf来更改内核参数。

nginx -s reload	使运行中的Nginx服务重新加载nginx.conf文件，Nginx会先检查新的配置项是否有误，如果全部正确就以“优雅”的方式关闭，再重新启动Nginx来实现这个目的。

nginx的访问日志access.log只包含http部分请求头信息，error.log则包含完全的数据。两者都会自动分片。

配置项
nginx部分配置项值支持正则

```
daemon on|off; # 默认on
error_log pathfile level; # level表示日志级别
include pathfile; # 将其他配置文件嵌入到当前的nginx.conf文件中
pid pathfile; # 保存master的pid文件路径。
user username groupname; # 设置master进程启动后，fork出的worker进程运行在哪个用户和用户组下。
worker_rlimit_nofile limit; #  worker进程可以打开的最大句柄描述符个数。
worker_connections limit; # 每个worker进程可以同时处理的最大连接数。

keepalive_timeout time; # 默认单位s，一个keepalive连接最大闲置时间。
client_max_body_size size; # 请求体最大值

listen port default_server; # default/default_server 将所在的server块作为整个Web服务的默认server块
server_name name; # 与header头中的Host匹配
location [ |~]/uri/{...} # ~区分大小写。location是有顺序的，当一个请求有可能匹配多个location时，只会被第一个location处理。
root path; # 资源路径
index file...; # 请求路径/时访问的页面（首页），依次查找path/file...
```

反向代理：nginx会完全的接收客户端的http包体后，再向上游服务器发起连接，发送http数据。
nginx的高性能是不是针对单个用户，而是服务器。

官方Nginx共有五大类型的模块：核心模块、配置模块、事件模块、HTTP模块、mail模块。

事件驱动架构，就是由一些事件发生源来产生事件，由一个或者多个事件收集器来收集、分发事件，然后许多事件处理器会注册自己感兴趣的事件，同时会“消费”这些事件。由阻塞变为非阻塞。
Nginx是完全的事件驱动架构，一般会由网卡、磁盘、定时器等产生事件（事件由内核产生），而事件模块将负责事件的收集、分发操作，而所有的模块都可能是事件消费者，它们首先需要向事件模块注册感兴趣的事件类型，这样，在有事件产生时，事件模块会把事件分发到相应的模块中进行处理。
事件驱动架构能减少进程对内存、CUP等资源的占用。
请求的多阶段异步处理：把一个请求的处理过程按照事件的触发方式划分为多个阶段，每个阶段都可以由事件收集、分发器来触发。
请求的多阶段异步处理能够避免进程休眠、减少进程数目、进程间切换。

使用一个master进程+多个worker进程的模式，worker进程的数量与服务器上的CPU核心数相等（类似go），真正地提供互联网服务。master进程则只负责监控管理worker进程、响应用户指令。
多个worker工作进程间通过进程间通信来实现负载均衡。
平台无关性：Nginx重新封装了日志、各种基本数据结构、常用算法等工具软件，在核心代码都使用了与操作系统无关的代码实现，在与操作系统相关的系统调用上则分别针对各个操作系统都有独立的实现。
Nginx支持多少种操作系统（包括支持哪些版本），就必须提供多少个TCP网络等事件驱动机制。

Nginx自己实现了定时器，不依赖操作系统。通过红黑树实现，轮询红黑树，将最左节点取出与当前时间比较，如果没有触发超时，就会知道要经过多久。

Linux解决了惊群以及epoll惊群，利用互斥标志位，一次只唤醒一个进程。
Nginx通过进程锁解决epoll惊群。

Nginx是web服务器，也可以作为反向代理、负载均衡器和http缓存。
Nginx特点：单次请求更快、模块化高拓展性、高可靠性、低内存消耗、高并发单机支持10万以上的并发连接、热部署master 管理进程与 worker工作进程的分离设计，使得 Nginx 能够支持热部署、开源
Apache:是一个重量级、不支持高并发的Web服务器，应为它是多进程的模式，高并发会消耗大量内存。

Nginx的FastCGI模块提供了fastcgi_param指令(conf/fastcgi.conf)，其主要完成的工作是将Nginx中的变量翻译成PHP中能够理解的变量。
描述nginx与php运行原理：

当 nginx reload 的时候 master 进程ID是没有变化的，worker 进程ID是有变化的。而nginx restart 的时候 master，worker的进程ID都有变化。reload 时，旧的worker进程和新的worker进程是共存的，旧的worker进程在处理完请求后会被杀掉。而 restart时，不会存在新旧共存的情况（master，worker 都是）。如果有请求未完成时，旧的master、worker 会有一段时间处理请求（超时时间）。然后被杀掉，创建新的master、worker 进程。所以 restart 的时候nginx 会有卡顿的现象。
// 重启php-fpm
php-fpm.conf 配置 process_control_timeout=5 master进程留给worker进程结束自己的时间
`kill -USR2 'cat /usr/local/php/var/run/php-fpm.pid'`

负载均衡策略：
轮询，服务器接收请求的比例是 1:1， 如果后端服务器down掉，能自动剔除
upstream web_servers {
   server localhost:8081;
   server localhost:8082;
}
权重，指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均
upstream test {
    server localhost:8081 weight=1;
    server localhost:8082 weight=3;
    server localhost:8083 weight=4 backup; # backup备份，其它服务器都失效时启用
}
ip_hash，每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，解决session等问题
upstream test {
    ip_hash;
    server localhost:8080;
    server localhost:8081;
}
fair（第三方），按后端服务器的响应时间来分配请求，响应时间短的优先分配
upstream backend {
    fair;
    server localhost:8080;
    server localhost:8081;
}
url_hash(第三方)，按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。
upstream backend {
    hash $request_uri;
    hash_method crc32;
    server localhost:8080;
    server localhost:8081;
}
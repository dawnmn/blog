Nginx是由c编写的。
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
    server localhost:8083 weight=4 backup;
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
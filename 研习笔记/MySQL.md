mysql由c和c++编写，服务器逻辑架构为：
* 服务层。客户端通信（消息体为字符串文本）、sql解析、查询优化等。
* 存储引擎层。innodb的索引、mvcc、锁、事务等。

**mysql.sock** 在UNIX系统下本地客户端连接MySQL服务器可以采用UNIX域套接字方式，这种方式需要一个套接字文件mysql.sock。这是因为UNIX域套接字速度比TCP/IP快。php-cgi.sock也是同理。
MySQL服务器会维持一个线程池，为每一个连接进来的客户端分配一个线程。

**my.cnf**
|   命令  |  类别   | my.cnf中能读取的组|
| --- | --- | --- |
| mysqld    |  启动服务器   |[mysqld] 、 [server] |
|  mysqld_safe   |   启动服务器  |[mysqld] 、 [server] 、 [mysqld_safe] |
|   mysql.server  |   启动服务器  | [mysqld] 、 [server] 、 [mysql.server]|
|  mysql   | 启动客户端    |[mysql] 、 [client] |
|  mysqladmin   |   启动客户端  | [mysqladmin] 、 [client]|
|   mysqldump  |   启动客户端  | [mysqldump] 、 [client]|




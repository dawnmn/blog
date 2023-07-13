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

系统变量的设置和查看，默认是SESSION。并不是所有系统变量都具有 GLOBAL 和 SESSION 的作用范围。启动选项和系统变量是互不包含的关系。
```
SET [GLOBAL|SESSION] 系统变量名=值;

SHOW [GLOBAL|SESSION] VARIABLES [LIKE 匹配的模式];
```
状态变量的查看，默认是SESSION：
```
SHOW [GLOBAL|SESSION] STATUS [LIKE 匹配的模式];
```
**utf8字符编码** utf8只是Unicode字符集的一种编码方案，Unicode字符集可以采用utf8、utf16、utf32这几种编码方案，utf8使用1~4个字节编码一个字符，utf16使用2个或4个字节编码一个字符，utf32使用4个字节编码一个字符。在MySQL中utf8是utf8mb3的别名，只使用1~3个字节表示字符。utf8mb4则是正宗的utf8字符集，使用1~4个字节表示字符。

**InnoDB 表对主键的生成策略** 优先使用用户自定义主键作为主键，如果用户没有定义主键，则选取一个 Unique 键作为主键，如果表中连 Unique 键都没有定义的话，则 InnoDB 会为表默认添加一个名为row_id 的隐藏列作为主键。InnoDB存储引擎会为每条记录都添加 transaction_id和 roll_pointer 这两个列，但是 row_id 是可选的（在没有自定义主键以及Unique键的情况下才会添加该列）。
**InnoDB 页**


InnoDB中一个页一般是 16KB ，当记录中的数据太多，当前页放不下的时候，会把多余的数据存储到其他页中，这种现象称为行溢出 。
页结构：
![](../images/innodb页结构.png)
|   名称  | 中文名    |    占用空间大小 |  简单描述   |
|-----|-----|-----|-----|
|  File Header   | 文件头部   |  38 字节   |  页的一些通用信息   |
|   Page Header  |   页面头部  |  56 字节   |   数据页专有的一些信息  |
|   Infimum + Supremum  |  最小记录和最大记录   |  26 字节   | 两个虚拟的行记录    |
|   User Records  |  用户记录   |  不确定   |  实际存储的行记录内容   |
|   Free Space  |   空闲空间  |   不确定  |  页中尚未使用的空间   |
|  Page Directory   |  页面目录   | 不确定    |   页中的某些记录的相对位置  |
|   File Trailer  |  文件尾部   |  8 字节   |  校验页是否完整   |


Compressed 行格式：
![](../images/innodb的compact行格式.png)
记录头结构：
|名称     | 大小(bit) | 描述                                         |
|-----|---------|--------------------------------------------|
|  预留位1   | 1       | 没有使用                                       |
|   预留位2  | 1       | 没有使用                                       |
|   delete_mask  | 1       | 标记该记录是否被删除                                 |
|  min_rec_mask   | 1       | B+树的每层非叶子节点中的最小记录都会添加该标记                   |
| n_owned    | 4       | 表示当前记录拥有的记录数                               |
|  heap_no   | 13      | 表示当前记录在记录堆的位置信息，从2开始自增（自动生成的0是最小记录、1是最大记录） |
|   record_type  | 3       | 表示当前记录的类型:0 普通记录；1 B+树非叶节点记录；2 最小记录；3 最大记录 |
|   next_record  | 16      |              从当前记录的真实数据到按照主键从小到大下一条记录的真实数据的地址偏移量                              |


Dynamic 和 Compressed 行格式与Compact 行格式很相似，只是在处理 行溢出 数据时不同。Compressed 行格式会采用压缩算法对页面进行压缩。


















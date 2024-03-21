postgresql是由C语言开发的，源于加州大学伯克利分校的研究项目，后被开源出来由社区维护。

**schema**（模式），一个数据库包含一个或多个已命名的模式，模式又包含表。模式还可以包含其它对象，包括数据类型、函数、操作符等。同一个对象名可以在不同的模式里使用而不会导致冲突。在数据库创建的同时，就已经默认为数据库创建了一个模式public，这也是该数据库的默认模式，public模式可以存储大家都需要访问的对象。相当于三级目录中的第二级：数据库/模式/表。

**角色与用户**的关系：对于PostgreSQL来说，这是完全相同的两个对象，只是角色创建时默认没有登录权限。通过ALTER ROLE custom LOGIN;就能登录。

**表空间**：表空间就是一个简单的目录，里面存储的是它所包含的数据库的各种物理文件。

表空间与数据库的关系：一个表空间可以存储多个数据库（虽然不建议如此，但却是可行的）。默认的数据库所有者是当前创建数据库的角色，默认的表空间是系统的默认表空间pg_default。在PostgreSQL中，数据的创建是通过克隆数据库模板来实现的。数据库创建时相对完整的语法应该是：CREATE DATABASE dbname OWNER kanon TEMPLATE template1 TABLESPACE tablespacename;

**表空间与表**的关系：一个表在创建的时候可以指定表空间。可以将表的数据存在一个较慢的磁盘上的表空间，而将表的索引存在于一个快速的磁盘上的表空间。

**聚合函数**不能被用于WHERE子句中。where在聚集函数之前，having在聚集函数之后。

引用标识符区分大小写。

count(*)得到输入行的总数。count(f1)得到输入行中f1为非空的数量，因为count忽略空值。而count(distinct f1)得到f1的非空可区分值的数量。

mysql、postgresql、oracle都支持SQL的典型功能和特征：外键、分页、聚合函数等。

postgresql的数据结构与mysql可以完全对应上。

`json 和 jsonb`数据类型接受几乎完全相同的值集合作为输入。主要的实际区别之一是效率。json数据类型存储输入文本的精准拷贝，处理函数必须在每次执行时必须重新解析该数据。而jsonb数据被存储在一种分解好的二进制格式中，它在输入时要稍慢一些，因为需要做附加的转换。但是jsonb在处理时要快很多，因为不需要解析。jsonb也支持索引

postgresql也是主要使用btree索引

**VACUUM**用于恢复表中死元组占用的空间。当记录被删除或更新（删除后插入）时，会创建一个死元组。PostgreSQL 不会从表中物理删除旧行，而是在其上放置一个标记，以便查询不会返回该行。当vacuum进程运行时，这些死元组占用的空间被标记为可被其他元组重用。
**ANALYZE**用于收集有关数据库中表内容的统计信息，统计结果存储在系统表PG_STATISTIC下。执行计划生成器会使用这些统计数据，以确定最有效的执行计划。可以通过指定table_name、column和partition_name参数把分析限定在特定的表、列或分区表中。需要定期执行。
Autovacuum能够自动执行VACUUM，Autovacuum不会恢复死元组占用的磁盘空间。当删除表的大部分数据后，执行VACUUM FULL以回收磁盘空间。

explain和explain analyze的区别：explain analyze会真正执行sql语句。

postgresql也使用了MVCC

EXPLAIN

EXPLAIN SELECT * FROM "abc";
Seq Scan on hehe2  (cost=0.00..160.77 rows=4977 width=141)

cost 0.00 返回第一行的耗时
cost 160.77 返回所有行的耗时
rows 返回的行数
width 每行占用的数据宽度（字节）


EXPLAIN ANALYSE SELECT * FROM "abc"
Seq Scan on hehe2  (cost=0.00..160.77 rows=4977 width=141) (actual time=0.021..0.895 rows=4977 loops=1)
Planning Time: 0.078 ms
Execution Time: 0.833 ms


EXPLAIN (ANALYSE,BUFFERS) SELECT * FROM "hehe2"
Buffers: shared hit=111

shared hit=111 在共享内存里面直接读到111个块；
read=xx 从磁盘中读了xx块
written=xx 写磁盘共xx块


BEGIN;
EXPLAIN ANALYZE ...;
ROLLBACK;
数据类型：`int` `float` `bool` `string` `object` `callable` `array ` `resource` `null`
值为false的情况: `''` `0` `0.0` `[]` `'0'` `null`
进制表达前缀：`0x` `0` `0b`
整数溢出转化为float，5/3结果为1.666...
浮点数转整数，向下取整
string是由字节组成的数组加上一个整数指明缓冲区的长度。可以把string当做字符组成的array，字符串会按照脚本文件的编码方式编码，`strlen` `mb_strlen`，字符串类型既是字符串也是byte，二进制字符串。
数组的key是int或者string，值可以是任意类型，如果数组定义中有多个相同的键，后面会覆盖前面的值，如果没指定键名，则为最大整数键名加一。

array是列表、散列表（哈希表）、队列、栈、集合、树形结构、多维数组，底层实现是散列表(HashTable结构体)。HashTable包含一些计数器和Bucket指针，Bucket包含Key，Key的哈希值和Zval，映射函数将key与value建立映射关系，可以根据key的哈希值与Bucket数组大小取模得到。哈希碰撞是指不同的key可能计算得到相同的哈希值，这时Bucket里面的zval里面的联合体里有一个指针会串成链表，查找时遍历链表比较key。

函数引用传递参数和返回引用，定义和使用都要带上&符号。
引用是用不同的名字访问同一个变量的内容，引用不是指针，不是内存地址，而是符号表别名，可以看作是linux的硬连接。
unset一个引用只是断开变量名与内容的联系，参考linux的硬连接。

`password_hash` `password_verify`

php的变量保存在zval结构体中，一个zval包含变量的类型和zend_value结构体（用于存放变量实际的value）、is_ref（区分普通变量和引用变量）、refcount（指向这个变量容器的变量个数）。
变量内存管理通过引用计数（变量赋值、传递时并不会直接硬拷贝）+写时复制。
并不是所有的php变量都会用到refcount，标量：true/false/double/long/null是硬拷贝不需要，interned string（可以理解为预定义字符串）也不需要。


|     type       | refcounted |
|----------------|------------|
|simple types    |            |
|string          |      Y     |
|interned string |            |
|array           |      Y     |
|immutable array |            |
|object          |      Y     |
|resource        |      Y     |
|reference       |      Y     |
写时复制：当一个变量试图更改value时候，会复制value并更改，断开旧的指向（针对string、array）。
变量回收：主动销毁（unset）、自动销毁（gc）。
目前垃圾只会出现在array、object两种类型中，所以只会针对这两种情况作特殊处理：当销毁一个变量时，如果发现减掉refcount后仍然大于0，且类型是IS_ARRAY、IS_OBJECT则将此value放入gc可能垃圾双向链表中，等这个缓冲buffer（zend_value的zend_refcounted_h结构体）达到一定数量后启动检查程序将所有变量检查一遍，如果确定是垃圾则销毁释放（深度优先遍历，把成员value的refcount减1，如果refcount为0，则清除）灰白黑。

xdebug_debug_zval('变量名')

php三大组成部分：
* sapi是php的接入层，将用户的输入引导至php脚本，包含cli、php-fpm。
* zend引擎，编译器（将php代码编译成可执行的opcodes），执行器（执行opcodes）
* 扩展

php不像go语言一样实现了http网络库，而是实现FastCGI协议，通过与web服务器配合来实现对http请求的处理。
fpm的实现就是创建一个master进程，在master进程中创建并监听socket，然后fork出多个子进程，这些子进程各自accept请求（请求不经过master），子进程的处理非常简单，它在启动后阻塞在accept上，有请求到达后开始读取请求数据，读取完成后开始处理然后再返回，在这期间是不会接收其它请求的，也就是说fpm的子进程同时只能响应一个请求，响应超时返回504。没有可用的worker时，master返回502。
fpm可以同时监听多个端口，每个端口对应一个worker pool。
static: 这种方式比较简单，在启动时master按照pm.max_children配置fork出相应数量的worker进程，即worker进程数是固定不变的
dynamic: 动态进程管理，首先在fpm启动时按照pm.start_servers初始化一定数量的worker，运行期间如果master发现空闲worker数低于pm.min_spare_servers配置数(表示请求比较多，worker处理不过来了)则会fork worker进程，但总的worker数不能超过pm.max_children，如果master发现空闲worker数超过了pm.max_spare_servers(表示闲着的worker太多了)则会杀掉一些worker，避免占用过多资源，master通过这4个值来控制worker数

TS指Thread Safety，即线程安全，针对多线程。
NTS 非线程安全，php-fpm是多进程单线程，因此适用。

命名空间：解决类、函数、常量、接口名字冲突。
类的自动加载：`spl_autoload_register`，可以支持任意数量的加载器，直到类成功注册为止，里面还是用的是require。常用框架比如yii2（有一套自己的autoload）也是借用composer+PSR规范实现框架外部类的自动加载，通过引入/vendor/autoload.php。
composer是php包管理器，也是自动加载的关键，它实现了类与文件的映射关系，并且使用spl_autoload_register在需要时载入内存。
psr4：命名空间必须对应一个目录，类名必须与php文件名相同，大小写敏感。

获取客户端真实IP：HTTP_X_ORIGINAL_FORWARDED_FOR HTTP_X_FORWARDED_FOR HTTP_CLIENT_IP REMOTE_ADDR
服务器真实IP：SERVER_ADDR

重定向：header函数（location） + exit()
BOM头是一串隐藏的字符,用于让记事本等编辑器识别这个文件是否以UTF-8编码，PHP不能识别，会把BOM作为文件开头正文的一部分。
if (substr($t, 0, 3) == '\xef\xbb\xbf') {
	$t = substr($t, 3);
}

PHP魔术方法：
__construct() __destruct() __set() __get() __isset() __isget() __sleep() __wakeup() __clone()

YII2 优点：结构清晰，组件齐全，能快速开发项目，特点：配置强大，自定义修改请求、响应、日志等，yii队列也很好用。依赖注入（Denpdency Injection, DI）和服务定位器（Service Locator）两种模式。

PHP7 性能提升的原因：程序运作时搬动的内存位数，存储变量的结构体变小、字符串结构体的改变、数组结构的改变。



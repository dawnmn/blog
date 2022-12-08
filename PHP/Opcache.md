opcache:
原理：将 PHP 编译产生的字节码（opcode）以及数据（Interned String：变量名称、类名、方法名、字符串、注释等）缓存到共享内存中。

开启opcache扩展
```
vim php.ini

zend_extension=opcache.so

php -m
```

配置opcache参数
```
;允许在 web 环境使用
opcache.enable=1
;允许在 cli 环境使用
opcache.enable_cli=1
;OPcache 的共享内存大小，以兆字节为单位。使用 opcache_get_status()['memory_usage'] Opcahce内存使用情况
opcache.memory_consumption=128
;用来存储预留字符串的内存大小，以兆字节为单位
opcache.interned_strings_buffer=8    
;OPcache 哈希表中可存储的PHP文件数量上限，find . -type f -print | grep php | wc -l 获取项目的php文件数目
opcache.max_accelerated_files=10000 
; 检查文件时间戳,来确定是否需要重新生成PHP脚本编译缓存，设置0表示不检查
opcache.validate_timestamps = 0
```

重启php-fpm
phpinfo()查看配置是否生效

提供一个接口，运行`opcache_reset()`函数，代码更新后，调用这个接口。不要在业务高峰期操作，会造成反复新建缓存。

通过jmeter接口简单测试，响应时间缩短了8倍，性能提升明显
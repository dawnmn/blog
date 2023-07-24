`sh`是Unix标准默认的shell，`bash`是Linux标准默认的shell，bash完全兼容sh。
`#!/bin/bash` 表示用/bin目录下的bash解释器来解释执行脚本。

Shell支持：`环境变量`、`用户自定义变量`。
**只读的环境变量**
| 环境变量 | 说明                                                          |
|------|-------------------------------------------------------------|
|   $0   | 当前脚本的文件名                                                    |
|   $num   | $1是第一个参数，$2是第二个参数                                           |
|   $#   | 传入脚本的参数的个数                                                  |
|   $*   | 所有的位置参数(作为单个字符串)                                            |
|   $@   | 所有的位置参数(每个都作为独立的字符串)。                                       |
|   $?   | 当前shell进程中，上一个命令的返回值，如果上一个命令成功执行则$?的值为0，否则为其他非零值，常用做if语句条件。 |
|   $$   | 当前shell进程的pid                                                            |
|   $!   | 后台运行的最后一个进程的pid                                                            |
|   $-   | 显示shell使用的当前选项                                                            |
|   $_   | 之前命令的最后一个参数                                                            |

**用户自定义变量**
```
myEmail="huoty@gmail.com"
echo $myEmail
unset myEmail
echo $myEmail
```
取子符串
```
var="12345678"
echo ${var:5}
echo${var:0:5}
```
计算字符串长度
```
var="12345678"
echo ${#var}  # 输出：8
```
替换或删除部分字符串
```
var="12341234"
echo ${var/1234/1111}  # 输出：11111234

var="12341234"
echo ${var//123/}  # 输出：44
```

**数组**
Bash支持一维数组（不支持多维数组），并且没有限定数组的大小，数组元素的下标由0开始编号。
```
NAME[0]="Zara"
NAME[1]="Qadir"
NAME[2]="Mahnaz"
NAME[3]="Ayan"
NAME[4]="Daisy"
echo "First Index: ${NAME[0]}"
echo "Second Index: ${NAME[1]}"
echo "Get All Method: ${NAME[*]}"
echo "Get All Method: ${NAME[@]}"
```
获取数组长度
```
length=${#array_name[@]}
```
**Shell的引号**
与php相似
双引号 ""
```
echo "My current directory is `pwd`"
echo "My home the directory is $HOME \n"
```
单引号 ''
不转义

倒引号 ``
倒引号括起来的字符串被 Shell 解释为命令行，在执行时，Shell 会先执行该命令行，并以它的标准输出结果取代整个倒引号部分。
```
mypath=`pwd`
echo My directory is $mypath
```





**shell**
用于登录的某个虚拟控制器终端，或在GUI中运行终端仿真器时所启动的默认的交互shell，是一个`父shell`。
进程就是正在运行的程序。`bash shell`是一个程序，当它运行的时候，就成为了一个进程。一个运行着的shell就是某种进程而已。
可以在子shell中不停的继续创建子shell，它们最终会形成一个嵌套结构，可以利用`exit`命令有条不紊地退出各个子 shell。
shell是弱类型语言。
显示出当前运行在后台模式中的所有用户的进程：`jobs -l`
查询历史命令，会区分用户：
```
history | grep type
history 30
```
**环境变量**
环境变量存储在内存中，分为全局变量和局部变量。全局环境变量对于shell会话和所有生成的子shell都是可见的。局部变量则只对创建它们的shell可见。
查看全局变量：`env`
增加全局变量，注意，只在当前shell进程中有效：
```
my_variable="I am Global now"
export my_variable
```
删除全局变量：
```
unset my_variable
```
如果要用到变量，使用$；如果要操作变量，不使用$。
修改子shell中全局环境变量并不会影响到父shell中该变量的值。子shell也不能通过export命令改变父shell中全局环境变量的值。
`PATH环境变量`，当你在shell命令行界面中输入一个外部命令时，shell必须搜索系统来找到对应的程序。PATH环境变量定义了用于进行命令和程序查找的目录。
`/etc/profile`文件是系统上默认的`bash shell`的主启动文件。系统上的每个用户登录时都会执行这个启动文件。
```
vim /etc/profile
source /etc/profile
```

执行文件中的shell命令：
```
source filename
```
数组变量：
```
hello=(1 3 'bb' 5)
echo ${hello[0]}
```
**权限管理**
Linux系统使用一个专门的文件来将用户的登录名匹配到对应的UID值（root的UID为0）。这个文件就是`/etc/passwd`文件，它包含了一些与用户有关的信息：
* 登录用户名
* 用户密码
* 用户账户的UID（数字形式）
* 用户账户的组 ID（GID）（数字形式）
* 用户账户的文本描述（称为备注字段）
* 用户HOME目录的位置
* 用户的默认shell

切换到root用户：
会重新加载/etc/profile文件等，执行后是完全的root环境。
```
sudo su - 
```
以root身份执行命令：
```
sudo command
```
创建用户：
-m命令行选项会使其创建 HOME 目录
```
useradd -m test
```


修改当前登录用户密码：
```
passwd
```
修改test用户的密码：
```
passwd test
```

修改用户所在的组：
```
usermod -g www test
```
禁用用户：
```
usermod -L test
```
解禁用户：
```
usermod -U test
```

删除用户：
```
userdel test
```
与用户账户类似，组信息也保存在系统的一个文件中：`/etc/group`。
创建用户组：
```
groupadd testg
```
修改用户组：
修改名称，由于所有的安全权限都是基于 GID 的，你可以随意改变组名而不会影响文件的安全性。
```
groupmod -n testgnew testg
```

查看用户所在的组：
查看当前登录用户
```
groups
```
查看test用户
```
groups test
```
查看创建文件和目录的默认权限：
`umask`是掩码值，通常为`0022`，文件一开始权限`666-022=644`，文件夹`777-022=755`。

所有者、所有者在的组、所有人
执行、写、读

改变文件所属关系：
改变属主和属组
```
chown www:test a.txt
```
只改变属组
```
chown :testgnew a.txt
```
**shell脚本**
脚本变量：
用户变量可以是任何由字母、数字或下划线组成的文本字符串，长度不超过 20 个。用户变量区分大小写，所以变量 Var1 和变量 var1 是不同的。 使用等号将值赋给用户变量。在变量、等号和值之间不能出现空格。
shell 脚本中定义的变量会一直保持着它们的值，但在 shell 脚本结束时会被删除掉。

命令替换：
可以从命令输出中提取信息，并将其赋给变量，两种方式。
```
testing=`date`
testing=$(date)
```
命令替换会创建一个子shell来运行对应的命令。子shell是由运行该脚本的shell所创建出来的一个独立的子shell。正因如此，由该子shell所执行命令是无法使用脚本中所创建的变量的。
```
#!/bin/bash
# copy the /usr/bin directory listing to a log file
today=$(date "+%Y-%m-%d %H:%M:%S")
ls /usr/bin -al > log.$today
```
覆盖输出
```
who > a.txt
```
追加输出
```
date >> a.txt
```

输入
```
wc < a.txt
```

管道
在命令之间，将一个命令的输出重定向到另一个命令中。不要以为由管道串起的两个命令会依次执行。Linux 系统实际上会同时运行这两个命令，在系统内部将它们连接起来。在第一个命令产生输出的同时，输出会被立即送给第二个命令。数据传输不会用到任何中间文件或缓冲区。
```
command1 | command2
```
shell数学运算符只支持整数运算，除法时只保留整数：
```
var1=$[1 + 5]
echo $var1
# 6
var2=$[$var1 * 2]
echo $var2
# 12
```
退出状态码：
是一个`0~255`的整数值，一个成功结束的命令的退出状态码是`0`。如果一个命令结束时有错误，退出状态码就是一个正数值。
`$?`用于保存上个已执行命令的退出状态码。
```
date
Sun Dec 20 01:35:39 PM CST 2020
echo $?
# 0
```
`exit`命令允许你在脚本结束时指定一个退出状态码
```
exit 5
```
shell保留了前三个文件描述符 `0 1 2`:
* 0 缩写:STDIN 含义:标准输入，< 输入重定向
* 1 缩写:STDOUT 含义:标准输出，默认输出到屏幕，> 输出重定向 >> 追加
* 2 缩写:STDERR 含义:标准错误，默认输出到屏幕，2> 输出重定向 2>> 追加

shell 通过特殊的STDERR文件描述符来处理错误消息。STDERR并不会随着STDOUT的重定向而发生改变，需要单独指定。

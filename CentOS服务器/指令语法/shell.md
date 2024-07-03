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

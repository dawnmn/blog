**windows安装jdk**
[官网下载](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
选择jdk，选择windows x64，下载并安装
配置环境变量：
新建系统变量：
|    变量名 |变量值     |
| --- | --- |
|   JAVA_HOME  |   C:\Program Files\Java\jdk-1.8  |
|   Path  |   增加一个值：%JAVA_HOME%\bin  |

**linux安装jdk**
[官网下载](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
选择jdk，选择linux x64

解压，移动到/usr/local
```
vim /etc/profile
export JAVA_HOME=/usr/local/jdk1.8.0_231  
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export  PATH=${JAVA_HOME}/bin:$PATH 

source /etc/profile

java -version
```



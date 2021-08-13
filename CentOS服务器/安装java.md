#### **安装jdk8**
[官网下载](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
选择jdk8，选择linux x64

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

#### **安装jdk11**
```
yum search java-11
yum install java-11-openjdk.x86_64

java -version
```


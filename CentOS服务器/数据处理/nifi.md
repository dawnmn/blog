**windows安装**
1. 安装最新版本的jdk（java环境）
2. [官网下载](https://nifi.apache.org/download.html)，选择`Binaries`->`Apache NiFi Binary 1.24.0`，下载后得到`nifi-1.24.0-bin.zip`，解压到安装目录。

修改JVM memory settings:
```
conf/bootstrap.conf

# JVM memory settings
java.arg.2=-Xms4g
java.arg.3=-Xmx8g
```
启动nifi：双击`bin/run-nifi.bat`文件，浏览器打开`https://localhost:8443/nifi/login`，账号密码在`logs/nifi-app.log`中搜索`username`。
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
关闭nifi：请选择已启动的窗口,按住Ctrl+C。

**研习笔记**
`Apache NiFi`是基于流程编程概念的数据流系统。

`FlowFile`：每条"用户数据"（即用户带进NiFi的需要进行处理和分发的数据）称为FlowFile。FlowFile由两部分组成：Attributes 和 Content。Content是用户数据本身。Attributes是与用户数据关联的键值对。

`Processor`：处理器,是NiFi组件,负责创建,发送,接收,转换,路由,拆分,合并和处理FlowFiles。它是NiFi用户可用于构建其数据流的最重要的构建块。

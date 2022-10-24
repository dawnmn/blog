#### 安装
参见[github](https://github.com/erikdubbelboer/phpRedisAdmin)，分别下载`phpRedisAdmin`和`predis`。
将predis移动到phpRedisAdmin，作为phpRedisAdmin的vendor。
```
cd includes
cp config.sample.inc.php config.inc.php
```
修改`host ` `port `等参数，注意`密码`是`auth`字段。
4. 配置nginx后即可访问。
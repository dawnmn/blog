#### 安装
1. 参见[github](https://github.com/erikdubbelboer/phpRedisAdmin)，分别下载`phpRedisAdmin`和`predis`。
2. 将predis移动到phpRedisAdmin，作为phpRedisAdmin的vendor。
3. 进入includes目录，复制`config.sample.inc.php` -> `config.inc.php`，可以在该文件修改redis的配置。
4. 配置nginx后即可访问。
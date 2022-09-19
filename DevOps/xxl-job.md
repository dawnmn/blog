[官网安装文档](https://www.xuxueli.com/xxl-job/#2.1%20%E5%88%9D%E5%A7%8B%E5%8C%96%E2%80%9C%E8%B0%83%E5%BA%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E2%80%9D)
[下载源码](https://gitee.com/xuxueli0323/xxl-job)

通过源码文件初始化数据库：`/xxl-job/doc/db/tables_xxl_job.sql`
修改配置：`/xxl-job/xxl-job-admin/src/main/resources/application.properties`
```
server.port=8091

spring.datasource.url=jdbc:mysql://192.168.126.128:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai
spring.datasource.username=root
spring.datasource.password=mypass
```
docker启动
```
docker pull xuxueli/xxl-job-admin:2.3.1
docker run -p 8091:8091 -v /tmp:/data/applogs --name xxl-job-admin -v /var/data/xxl-job-admin/application.properties:/application.properties -e PARAMS='--spring.config.location=/application.properties' -d xuxueli/xxl-job-admin:2.3.1
```
浏览器访问
http://192.168.126.128:8091/xxl-job-admin/
`admin` `123456`
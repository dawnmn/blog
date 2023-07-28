参见[官网](https://apisix.apache.org/zh/docs/apisix/installation-guide/)，选择docker安装
浏览器访问`http://192.168.126.128:9000`，默认账号密码 `admin` `admin`
接口访问`http://192.168.126.128:9080/admin/login`

访问日志路径
```
docker exec -it docker-apisix-apisix-1 bash
vi logs/access.log
```
启动
```
docker compose -p docker-apisix up -d
```
配置HTTPS
1. 用`cert.go`生成密钥，`host`为：`www.ddd.com`
2. `apisix网站后台`->`证书`-> `cert.pem`、`key.pem`
3. 本地vhost 配置 `192.168.2.70 www.ddd.com`

4. `api`访问：`https://www.ddd.com:9443/xxx`

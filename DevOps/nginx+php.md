**安装nginx**
```
docker pull nginx
# 检查是否成功
docker run --name nginx -p 9601:80 -d nginx
# 访问 http://192.168.152.134:9601
docker stop nginx
docker rm nginx
```
**安装php**
```
docker pull php:7.3.30-fpm

docker run -d -v /var/www:/var/www/html -p 9000:9000 --name phpfpm73 php:7.3.30-fpm
```
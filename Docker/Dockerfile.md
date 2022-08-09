```
docker pull nginx

docker run --name mynginx -d -p 9601:80 nginx
#浏览器打开localhost:9601
docker exec -it myserver bash
echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
#页面已经变化，删除容器
docker rm containerid

#空文件夹下创建Dockerfile
FROM nginx
RUN echo '你好' > /usr/share/nginx/html/index.html
# 创建镜像
docker build -t nginx:v3 .
docker image ls
# 启动新的镜像的容器
docker run --name mynginx -d -p 9601:80 nginx:v3
```
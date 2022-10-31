**docker 安装**
```
docker pull rabbitmq:3.9-management

docker run -p 15672:15672 -p 5672:5672 -p 1883:1883 -d --hostname my-rabbit --name some-rabbit rabbitmq:3.9-management
```
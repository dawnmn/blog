#### 安装
https://hub.docker.com/r/sebp/elk
```
https://hub.docker.com/r/sebp/elk

docker pull sebp/elk
sysctl -a | grep vm.max_map_count
sysctl -w vm.max_map_count=262144
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -it --name elk sebp/elk

# 安装与elk相同版本的filebeat
docker pull elastic/filebeat:7.13.2
```
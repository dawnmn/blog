```
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.2.3
docker network create elastic

mkdir -p /root/data/elasticsearch/data
chown -R 777 /root/data/elasticsearch/data

docker run --name es --net elastic -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms512m -Xmx512m" docker.elastic.co/elasticsearch/elasticsearch:8.2.3

# 账号elastic 密码配置
docker exec -it es bash
elasticsearch-setup-passwords auto
# 记住自动生成的密码

```
浏览器访问`https://192.168.126.128:9200/`
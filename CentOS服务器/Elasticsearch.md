docker安装
```
docker run --name es --net elastic -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms512m -Xmx512m" docker.elastic.co/elasticsearch/elasticsearch:7.16.3
```
浏览器访问`http://192.168.126.128:9200/`
go包引入`go get github.com/elastic/go-elasticsearch/v7@v7.16`
```
curl -X GET "localhost:9200/?pretty"
```
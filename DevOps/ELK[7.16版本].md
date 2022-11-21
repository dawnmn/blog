**安装sebp/elk**
https://hub.docker.com/r/sebp/elk
```

docker pull sebp/elk:7.16.0
sysctl -a | grep vm.max_map_count
sysctl -w vm.max_map_count=262144
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -it --name elk sebp/elk:7.16.0
```
查看`http://192.168.126.129:9200/` `http://192.168.126.129:5601/`

**安装filebeat，配置filebeat（以yii2日志为例）**
[官网下载filebeat-7.16.0-linux-x86_64.tar.gz](https://www.elastic.co/cn/downloads/past-releases#filebeat)，解压完进入目录。
```
vim filebeat.yml
```
```
# ============================== Filebeat inputs ===============================

filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /home/wwwroot/auth/backend/runtime/logs/*.log
  multiline.type: pattern
  multiline.pattern: ^\d{4}-\d{2}-\d{2}[\s]+\d{2}:\d{2}:\d{2}
  multiline.negate: true
  multiline.match: after
# =================================== Kibana ===================================
setup.kibana:
  host: "192.168.126.129:5601"

# ======================= Elasticsearch template setting =======================

setup.template.name: "auth-backend"
setup.template.pattern: "auth-backend-*"
setup.template.overwrite: true
setup.template.enabled: false

setup.ilm.enabled: false

# ================================== Outputs ===================================
output.elasticsearch:
  hosts: ["192.168.126.129:9200"]

  username: "elastic"
  password: "changeme"

  index: "rrm-backend-%{+yyyy.MM.dd}"
```
**启动filebeat**
```
# 初始化filebeat并启动
./filebeat setup
./filebeat -e

# elasticsearch查看rrm-backend-\*索引是否生效
curl -X GET "192.168.126.129:9200/\_cat/indices?pretty"
```
kibana创建查询索引：kibana -> create index pattern -> `rrm-backend*` `@timestamp`

成功示例
![](../images/elk展示.jpg)


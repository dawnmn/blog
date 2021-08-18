#### 安装
[官网下载](https://www.mongodb.com/try/download/community)
选择4.4.8、RedHat / CentOS 7.0、server 下载
```
yum localinstall mongodb-org-server-4.4.8-1.el7.x86_64.rpm

mkdir -p /var/lib/mongo
mkdir -p /var/log/mongodb
chown -R mongod:mongod /var/lib/mongo
chown -R mongod:mongod /var/log/mongodb
```
```
vim /etc/mongod.conf
net:
  port: 27017
  bindIp: 0.0.0.0
```
```
vim /etc/systemd/system/mongodb.service
[Unit]
Description=High-performance, schema-free document-oriented database
After=network.target

[Service]
User=mongodb
ExecStart=/usr/bin/mongod --quiet --config /etc/mongod.conf

[Install]
WantedBy=multi-user.target
```
```
systemctl enable mongod.service
systemctl daemon-reload
service mongod start

chown mongod:mongod /tmp/mongodb-27017.sock
```
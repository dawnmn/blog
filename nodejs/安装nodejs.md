#### 官网安装
[官网](https://nodejs.org/zh-cn/download/)选择`Linux 二进制文件 (x64)` 下载
```
tar -xJvf node-x-x.tar.xz
mkdir /usr/local/nodejs
mv node-v14.15.0-linux-x64/* /usr/local/nodejs

vim /etc/profile
export PATH=/usr/local/nodejs/bin:$PATH
source /etc/profile

node -v
npm version
npx -v
```

#### yum安装
```
curl --silent --location https://rpm.nodesource.com/setup_14.x | sudo bash
yum install nodejs
```
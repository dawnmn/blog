## windows
#### 安装Go
[官网下载并安装](https://golang.org/dl/)
```
go version
go env
```
Windows环境变量里 [配置Go模块代理](https://goproxy.cn/)

#### 安装VS Code
[官网下载并安装]([https://code.visualstudio.com/](https://code.visualstudio.com/))
安装以下go扩展
![](../images/微信图片_20201013153210.png)
ctrl+shift+p，输入`Go:Install/Update Tools`，选择全部并安装

## linux
```
wget https://studygolang.com/dl/golang/go1.13.1.linux-amd64.tar.gz
tar -zxvf go1.13.1.linux-amd64.tar.gz
mv go/ /usr/local/

vim /etc/profile

export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
export GO111MODULE=on
export GOPROXY=https://goproxy.cn

source /etc/profile

go version

```
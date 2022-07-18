#### windows安装protobuf
[Github下载protobuf]([https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases))，选择protoc-xx.x-win64.zip，解压缩，将protoc.exe移动到go/bin目录下
```
protoc --version

protoc --go_out=plugins=grpc:. *.proto
```


#### linux安装protobuf
[Github下载protobuf]([https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases))，选择protobuf-all-xxx.tar.gz
```
tar -zxvf protobuf-all-xxx.tar.gz
cd protobuf-xxx/
./configure
make
make install

ldconfig

protoc --version

cp /root/go/bin/protoc-gen-go /usr/local/go/bin
```
#### 配置gRPC
```
go get -u google.golang.org/grpc


# 在proto文件夹下执行
protoc --go_out=plugins=grpc:. *.proto
```
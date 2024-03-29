GRPC的两个特性：`多路复用`（一个客户端连接可以在多个客户端函数中使用）和`超时重连`。
单个连接的client可以支持最大到9w的qps，通过使用自建连接池，经测qps可以达到40w左右。
*****
官网参考：[grpc官网](https://grpc.io/docs/languages/go/basics/)、[Go示例](https://github.com/grpc/grpc-go)、[protobuf官网](https://protobuf.dev/programming-guides/proto3/#scalar)
**windows安装protobuf**
[Github下载protobuf]([https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases))，选择protoc-xx.x-win64.zip，解压缩，将protoc.exe移动到GOROOT/bin目录下
**linux安装protobuf**
[Github下载protobuf](https://github.com/protocolbuffers/protobuf/releases)，选择protobuf-all-xxx.tar.gz，注意：需要选择含有`all`的发布版本的源码安装包。参考[grpc官网](https://grpc.io/docs/languages/go/basics/)
```
tar -zxvf protobuf-all-xxx.tar.gz
cd protobuf-xxx/
./configure
make && make install && ldconfig

go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2
```
**使用protobuf**
编辑`proto/service_center.proto`文件：
```
syntax = "proto3";

package proto;
option go_package="./proto";

service ServiceCenter{
  rpc allocSchedServAddr(TaskId) returns(Addr);
}

message TaskId {
  int64 task_id = 1;
}

message Addr {
  string addr = 1;
}
```

```
# 查看版本
protoc --version

# 编译service_center.proto文件，先进入service_center.proto所在的目录proto
cd proto
protoc --go_out=. --go_opt=paths=source_relative --go-grpc_out=. --go-grpc_opt=paths=source_relative service_center.proto
```
**编码解码**
```
out, err := proto.Marshal(&message)
proto.Unmarshal(out, &message)
```
**示例**
官方参考：[Go示例](https://github.com/grpc/grpc-go)
server
```
package main

import (
   "context"
   "google.golang.org/grpc"
   "net"
   "strconv"
   pb "test/user"
)

type Server struct {
   pb.UnimplementedUserInfoServer
   userMap map[string]*pb.User
}

func (s *Server) AddUser(ctx context.Context, u *pb.User) (id *pb.UserId, e error) {
   if s.userMap == nil {
      s.userMap = map[string]*pb.User{}
   }

   uid := strconv.Itoa(len(s.userMap))
   s.userMap[uid] = u
   id = &pb.UserId{}
   id.Value = uid
   return
}

func (s *Server) GetUser(ctx context.Context, id *pb.UserId) (u *pb.User, e error) {
   if s.userMap == nil {
      s.userMap = map[string]*pb.User{}
   }
   u = s.userMap[id.Value]
   return
}

func main() {
   address := ":10010"
   listener, _ := net.Listen("tcp", address)

   s := grpc.NewServer()
   pb.RegisterUserInfoServer(s, &Server{})
   s.Serve(listener)
}
```
client
```
package main

import (
   "context"
   "fmt"
   "google.golang.org/grpc"
   "google.golang.org/grpc/credentials/insecure"
   pb "test/user"
   "time"
)

func main() {
   address := ":10010"
   conn, _ := grpc.Dial(address, grpc.WithTransportCredentials(insecure.NewCredentials()))

   client := pb.NewUserInfoClient(conn)
   defer conn.Close()

   ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
   defer cancel()
   u := &pb.User{Name: "jack", Description: "strong"}
   userId, _ := client.AddUser(ctx, u)
   uu, _ := client.GetUser(ctx, userId)
   fmt.Println(uu)
}
```

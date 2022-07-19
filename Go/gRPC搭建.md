#### windows安装protobuf
[Github下载protobuf]([https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases))，选择protoc-xx.x-win64.zip，解压缩，将protoc.exe移动到go/bin目录下
```
protoc --version

# a.proto增加
option go_package = "./";

protoc --go_out=plugins=grpc:./ a.proto
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
#### 示例
user.proto
```
syntax = "proto3";

package user;
option go_package="./";

service UserInfo{
  rpc addUser(User) returns(UserId);
  rpc getUser(UserId) returns(User);
}

message User {
  string id = 1;
  string name = 2;
  string description = 3;
}

message UserId{
  string Value = 1;
}

message UserList{
  repeated User list = 1;
}
```
server
```
package main

import (
   "context"
   "google.golang.org/grpc"
   "net"
   "strconv"
   "test/user"
)

type Server struct {
   userMap map[string]*user.User
}

func (s *Server) AddUser(ctx context.Context, u *user.User) (id *user.UserId, e error) {
   if(s.userMap == nil){
      s.userMap = map[string]*user.User{}
   }

   uid := strconv.Itoa(len(s.userMap))
   s.userMap[uid] = u
   id = &user.UserId{}
   id.Value = uid
   return
}

func (s *Server) GetUser(ctx context.Context, id *user.UserId) (u *user.User, e error) {
   if(s.userMap == nil){
      s.userMap = map[string]*user.User{}
   }
   u = s.userMap[id.Value]
   return
}

func main()  {
   address := "192.168.2.64:10010"
   listener,_ := net.Listen("tcp", address)

   s := grpc.NewServer()
   user.RegisterUserInfoServer(s, &Server{})
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
   "test/user"
   "time"
)

func main()  {
   address := "192.168.2.64:10010"
   conn,_ := grpc.Dial(address, grpc.WithTransportCredentials(insecure.NewCredentials()))

   client := user.NewUserInfoClient(conn)
   defer conn.Close()

   ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
   defer cancel()
   u := &user.User{Name: "jack", Description: "strong"}
   userId, _ := client.AddUser(ctx, u)
   uu, _ := client.GetUser(ctx, userId)
   fmt.Println(uu)
}
```
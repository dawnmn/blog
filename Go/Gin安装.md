在任意目录初始化gin
```
mkdir gin-example && cd gin-example

go env -w GO111MODULE=on

go env -w GOPROXY=https://goproxy.cn,direct

go mod init gin-example

go get -u github.com/gin-gonic/gin
```
创建一个test.go文件
```
package main

import "github.com/gin-gonic/gin"

func main() {
  r := gin.Default()
  r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{
      "message": "pong",
    })
  })
  r.Run() // listen and serve on 0.0.0.0:8080
}
```
运行
```
go run test.go
```

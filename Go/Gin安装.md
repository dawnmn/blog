使用Go Modules
```
go env -w GO111MODULE=on
```
在go/src/test目录下
```
go mod init test
```
test文件夹内就会生成一个go.mod文件。在test文件夹下新建test.go
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
项目所需要的第三方包都会通过go module来下载管理放在GOPATH/pkg/mod
```
go build
go run test.go
```
在浏览器输入localhost:8080/ping
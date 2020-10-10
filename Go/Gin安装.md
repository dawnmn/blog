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

go module vscode 代码提示
```
go install github.com/Go-zh/tools/cmd/gopls
```
file->preferences->setting 输入go，选择setting.json，添加：
```
"go.useLanguageServer": true,
"[go]": {
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.organizeImports": true,
    },
    // Optional: Disable snippets, as they conflict with completion ranking.
    "editor.snippetSuggestions": "none",
},
"[go.mod]": {
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.organizeImports": true,
    },
},
"gopls": {
     // Add parameter placeholders when completing a function.
    "usePlaceholders": true,

    // If true, enable additional analyses with staticcheck.
    // Warning: This will significantly increase memory usage.
    "staticcheck": false,
}
```
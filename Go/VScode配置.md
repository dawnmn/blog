#### 安装Go
[官网下载并安装](https://golang.org/dl/)
```
go version

go env
```
系统环境变量配置GOPATH为你自己的路径

重启电脑
```
go env
#确认GOPATH为自定义的
```
go get 获取远程包
```
go get github.com/go-sql-driver/mysql
```

#### 安装VS Code
[官网下载并安装]([https://code.visualstudio.com/](https://code.visualstudio.com/))
安装go扩展
![](images/vscode_plugin.png)

配置go插件代理
```
go env -w GOPROXY=https://goproxy.cn,direct
```
ctrl+shift+p，输入`Go:Install/Update Tools`，选择全部并安装
![](images/go-test.png)

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


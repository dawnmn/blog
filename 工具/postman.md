**接口文档导出成md**
下载[docgen](https://github.com/thedevsaddam/docgen)，选择`windows_amd64.exe`。postman导出v2版本json文件，执行以下指令：
```
./windows_amd64.exe build -i C:/Users/dawnmn/Desktop/新型城域网.postman_collection.json -o C:/Users/dawnmn/Desktop/index.md -m
```
如果报`parsing json filejson: cannot unmarshal string into Go struct field Request.item.item.request.url of type collection.URL`的错误，将postman导出v2版本json文件正则替换`"http\:\/\/192\.168\.2\.64\:9701.+"`成`{}`。
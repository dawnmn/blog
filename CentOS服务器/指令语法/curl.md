

**打印日志，跟随重定向**
`-Lv` `-v` `-L` `--verbose` `--location` 

**HTTP方法**
GET是默认值, 使用`-d`或`-F`使它成为一个POST，`-I`生成一个HEAD，`-T`发送一个PUT，`-X`指定方法，`-X DELETE`

**请求头**
```
-H 'Content-Type: application/json' --header 'Content-Type: application/json'
```

**body数据，自动转POST**
```
-d haha -d 'haha' -d "haha" --data haha
```

**表单文件上传**
`-F` `--form`
```
curl --location 'http://192.168.2.70:9080/auth-mng/general/file-upload' \
--header 'Api-Platform: BKhEb2u7D2h3C1Djt3WvVTeLsk2UMOz9' \
--header 'Authorization: Bearer 2sWzd6DztfW2s10KNourgOHkAmRBNB9Qj' \
--form 'file=@"/var/data/robot-server-v2/test.txt";category="txt"'
```

**下载文件**
```
-o test.txt -o /tmp/test.txt --output test.txt -O --remote-name
```


**整个请求的最大时间**
`-m` `--max-time`

**建立连接的最大时间**
`--connect-timeout`

**代理**
```
curl -x 127.0.0.1:29034 http://135.0.120.180:9002
curl -x 192.168.2.64:29034 http://135.0.120.180:9002/user/login?redirect=%2Froutes%2Flist
```

**退出状态**
```
#!/bin/sh
curl -x 127.0.0.1:29034 http://135.0.120.180:9002
res=$?
if test "$res" != "0"; then
   echo "the curl command failed with: $res"
fi
```

**复制**
浏览器访问时，可以复制成cURL的格式代码。

**压缩传输**
`--compressed`

**POST转GET请求**
```
# http://192.168.2.70:9080/auth/user/login-key?a=100
curl -d a=100 -G http://192.168.2.70:9080/auth/user/login-key
curl -d a=100 --get http://192.168.2.70:9080/auth/user/login-key
# http://192.168.2.70:9080/auth/user/login-key?a=100%20200
curl --data-urlencode "a=100 200" -G http://192.168.2.70:9080/auth/user/login-key
```

**跳过SSL检测**
`-k`

**用户名和密码**
```
curl -u 'bob:12345' https://google.com/login
```
上面命令设置用户名为bob，密码为12345，然后将其转为 HTTP 标头Authorization: Basic Ym9iOjEyMzQ1。

**发送Cookie**
```
curl -b 'foo1=bar;foo2=bar2' https://google.com
```
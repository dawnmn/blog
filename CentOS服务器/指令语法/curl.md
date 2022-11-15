不带有任何参数时，发出 GET 请求。
`curl https://www.example.com`
<br/>


`-A `参数指定客户端的用户代理标头，即User-Agent。curl 的默认用户代理字符串是curl/[version]。也可以通过-H参数直接指定标头，更改User-Agent
`curl -H 'User-Agent: php/1.0' https://google.com`
<br/>


`-b`参数用来向服务器发送 Cookie。
`curl -b 'foo1=bar;foo2=bar2' https://google.com`
<br/>


`-d`参数用于发送 POST 请求的数据体。
`curl -d'login=emma&password=123'-X POST https://google.com/login`
使用-d参数以后，HTTP 请求会自动加上标头Content-Type : application/x-www-form-urlencoded。
<br/>


`-F`参数用来向服务器上传二进制文件。
`curl -F 'file=@photo.png;type=image/png' https://google.com/profile`
上面命令会给 HTTP 请求加上标头Content-Type: multipart/form-data。
<br/>


`-i`参数打印出服务器回应的 HTTP 标头。
`curl -i https://www.example.com`
<br/>


`-k`参数指定跳过 SSL 检测。
<br/>


`-o`参数将服务器的回应保存成文件，等同于wget命令。
`curl -o example.html https://www.example.com`
<br/>


`-O`参数将服务器回应保存成文件，并将 URL 的最后部分当作文件名。
`curl -O https://www.example.com/foo/bar.html`
<br/>


`-u`参数用来设置服务器认证的用户名和密码。
`curl -u 'bob:12345' https://google.com/login`

上面命令设置用户名为bob，密码为12345，然后将其转为 HTTP 标头Authorization: Basic Ym9iOjEyMzQ1。

<br/>
`-X`参数指定 HTTP 请求的方法。
`curl -X POST https://www.example.com`
<br/>


`--connect-timeout`参数指定 HTTP 请求连接超时时间
`-m`参数指定 HTTP 请求数据传输的最大允许时间
`curl --connect-timeout 10 -m 20 "http://XXXXXXX"`
#### **场景**
HTTP协议是无状态的，服务器不能保持上下文去辨识和区分不同客户端。常用 的方式有：SESSION（有状态），JWT（无状态）
#### **SESSION**
cookie作为HTTP请求的一部分，在每次请求中传递，客户端和服务器都能对它进行修改，并且客户端能够存储cookie数据。实现步骤如下：
1. 客户端向服务器发送用户登录信息的POST请求。
2. 服务器给客户端分配SESSION ID，认证通过后将用户认证状态绑定到SESSION ID并存储。向客户端响应请求时在cookie中携带SESSION ID。
3. 客户端（浏览器）会自动存储cookie，并在每次请求中都携带cookie。因此，下次请求时也会携带SESSION ID，服务器通过验证SESSION ID识别用户和认证状态。

缺点
服务器集群或跨域时要求每台服务器都能够访问SESSION ID，因此需要数据库或缓存服务器保存SESSION ID。

#### **JWT**
JWT（Json Web Token），基于Json的协议，默认不加密，需要HTTPS协议。服务器认证通过后，向客户端发送一个特定数据结构的Token（形如Header.Payload.Signature），客户端每次请求都会携带该Token，服务器完全只靠这个对象认证用户身份。

数据结构：
* Header（头部）
* Payload（负载）
* Signature（签名）

Header
```
{
  "alg": "HS256", // 签名加密方式
  "typ": "JWT"    // 默认字段
}
```
将以上Json对象Base64编码后得到Header字符串。
Payload
```
{
  "iat": "...", // 签发时间
  "nbf": "...",   // 生效时间
  "exp": "...", // 过期时间
  "name": "...", // 自定义字段，不要存储敏感数据
  ...
}
```
将以上Json对象Base64编码后得到Payload字符串。
Signature
将Header字符串、Payload字符串、密钥（存储在服务器）用Header里面的签名加密方式加密。

优点
服务器是无状态的，从而比较容易实现扩展。
缺点
Token一旦发放无法废止或更改，除非服务器有专门处理的逻辑。


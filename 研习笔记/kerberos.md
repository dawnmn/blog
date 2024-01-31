Kerberos协议起源于美国麻省理工学院，基于公私钥加密体制，为分布式环境提供双向验证。Kerberos提供了一种单点登录 (Single Sign-On, SSO)的方法。不是让每个业务服务器自己实现一套认证系统，而是提供一个中心认证服务器(Authentication Server, AS)供这些服务器使用。

Kerberos协议是一个基于票据(Ticket)的系统，包含三个角色：
* 客户端（client）：发送请求的一方
* 服务端（Server）：接收请求的一方，业务服务器
* 密钥分发中心（Key Distribution Center，KDC）
密钥分发中心分为两部分：
* AS（Authentication Server）：认证服务器，专门用来认证客户端的身份并发放客户用于访问TGS的TGT（票据授予票据）。
* TGS（Ticket Granting Ticket）：票据授予服务器，用来发放整个认证过程以及客户端访问服务端时所需的服务授予票据（Ticket）
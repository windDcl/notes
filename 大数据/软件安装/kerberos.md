
>kerberos是什么？
>具体原理是啥？
>为什么它是一种成熟可靠的身份认证协议？

参考： https://zhuanlan.zhihu.com/p/266491528

![[Pasted image 20230817204526.png]]


## 简介
- kerberos是一种基于ticket进行身份认证的一种认证用户和服务的手段。
- 客户端将**依次**与 Authentication Service, Ticket Granting Service 以及目标Service进行交互，共三次交互。
- 客户端的每次交互都能收到两条消息，只有一条能够解密出来。
- 服务器收到客户端的请求，并不会直接和KDC交互，而是看能否正确解密出客户端的请求。

## 角色
### 1.principal
是kerberos世界里的用户，比如部署在hadoop01上的hive2服务在kerberos里的用户名就是 `hiveserer2/hadoop01@EXAMPLE.COM`
用户tree在kerberos里就是 `tree@EXAMPLE.COM`。
EXAMPLE.COM是kerberos的realm。用来区分不同的kerberos。
hiveserer2和tree在这里叫primary。hadoop01是instance。

- keytab：密码本，包含了多个principal与密码的文件，用户可以用它来认证
- ticket cache：客户端和KDC认证后生成的认证信息的文件，短期有效
- Realm：kerberos里的名称空间，不同的kerberos可以通过它来区分

### 2.KDC

见上面的图。
- Kerberos Database: 包含了一个 Realm 中所有的 principal、密码与其他信息。（默认：Berkeley DB）
- Authentication Service(AS认证服务): 进行用户信息认证，为客户端提供 Ticket Granting Tickets(TGT发票据)。  
- Ticket Granting Service(TGS验票据): 验证 TGT 与 Authenticator，为客户端提供 Service Tickets（访问server的票）。

## 以http服务为例查看过程




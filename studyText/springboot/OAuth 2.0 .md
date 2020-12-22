#  [OAuth 2.0 ](https://github.com/spring-projects/spring-security/wiki/OAuth-2.0-Migration-Guide)

**OAuth 2.0 规定了四种获得令牌的流程。你可以选择最适合自己的那一种，向第三方应用颁发令牌**

[详情链接]: http://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html



## 1.授权码（authorization code）

**指的是第三方应用先申请一个授权码，然后再用该码获取令牌。**这种方式是最常用的流程，安全性也最高，它适用于那些有后端的 Web 应用。

## 2.隐藏式（implicit）

有些 Web 应用是纯前端应用，没有后端。这时就不能用上面的方式了，必须将令牌储存在前端。

## 3.密码式（password）

**如果你高度信任某个应用，RFC 6749 也允许用户把用户名和密码，直接告诉该应用。该应用就使用你的密码，申请令牌，这种方式称为"密码式"（password）。**

## 4.客户端凭证（client credentials）

**最后一种方式是凭证式（client credentials），适用于没有前端的命令行应用，即在命令行下请求令牌。**
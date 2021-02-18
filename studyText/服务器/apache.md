**Apache HTTP** **[Server](https://baike.baidu.com/item/Server)**（简称**Apache**,Apache(音译为[阿帕奇](https://baike.baidu.com/item/阿帕奇/374191)))是最流行的Web服务器端软件之一.

Apache是以[进程](https://baike.baidu.com/item/进程)为基础的结构，进程要比[线程](https://baike.baidu.com/item/线程)消耗更多的系统开支，不太适合于[多处理器](https://baike.baidu.com/item/多处理器)环境，因此，在一个Apache Web站点扩容时，通常是增加[服务器](https://baike.baidu.com/item/服务器)或扩充群集节点而不是增加[处理器](https://baike.baidu.com/item/处理器)。

Apache[web服务器](https://baike.baidu.com/item/web服务器)软件拥有以下特性：

1.支持最新的[HTTP](https://baike.baidu.com/item/HTTP)/1.1通信协议

2.拥有简单而强有力的基于文件的配置过程

3.支持通用网关接口

4.支持基于IP和基于域名的虚拟主机

5.支持多种方式的[HTTP](https://baike.baidu.com/item/HTTP)认证

6.集成[Perl](https://baike.baidu.com/item/Perl)处理模块

7.集成[代理服务器](https://baike.baidu.com/item/代理服务器)模块

8.支持实时监视服务器状态和定制服务器日志

9.支持服务器端包含指令(SSI)

10.支持安全[Socket](https://baike.baidu.com/item/Socket)层(SSL)

11.提供用户会话过程的跟踪

12.支持FastCGI

13.通过[第三方](https://baike.baidu.com/item/第三方)模块可以支持[Java](https://baike.baidu.com/item/Java/85979)Servlets

## 相关模块

**1.SSO Module - LemonLDAP** 

LemonLdap 是 Apache 的一个实现了 Web SSO 的模块，可处理超过 20 万的用户。

**2.并发限制模块 - limitipconn**

limitipconn 是一个 Apache 的模块，用来限制每个 IP 的并发连接数。支持 Apache 1.x 和 2.x。

**3.日志监控模块**

Apache Live Log 是一个 Perl 编写的模块，可以在浏览器上直接实时的通过 Ajax 技术浏览和监控 Apache 的 日志文件。

**4.负载均衡模块**

mod_backhand 是一个Apache 的负载平衡模块 。它定义了每个请求的HTTP重定向在一个异构的Apache服务器群集。每个请求的处理，并贯穿了一套“候选人的职能” ，以确定哪些服务器是最适合的回应。请求然后代理到该服务器。设施已到位，让你写您自己的动态加载决策算法。一切有关的要求和当前可用的资源可用于决策过程。

**5.图像处理模块**

mod_gfx 是一个对图像进行即时处理的 Apache 模块，提供很多灵活的接口，包括：

Resizing

Resampling

Watermarking

Cropping

以后还将添加如下功能：

Add Text

Rotate

Draw Polygons

**6. 压缩模块**

mod-gzip-disk 是一个使用磁盘进行存储预压缩页面的 Apache 模块，与 mod-gzip 不同的是不需要每次请求的时候重新压缩。

使用方法:

gunzip -c mod_gzip_disk-0.5.tar.gz | tar -xvpf -

cd mod_gzip_disk

sudo make module

**7. 音乐模块**

mod_musicindex 是一个 Apache 用来处理音频文件的模块，类似 Perl 的 Apache::MP3，支持音频格式包括：MP3, Ogg Vorbis, FLAC, or MP4 / AAC ，可根据不同的音频属性进行排序列表、在线播放、下载、构建播放列表和搜索等，提供 RSS 和 Podcast 输出，支持多 CSS 和包下载。

**8.LDAP 认证模块**

LDAP 是轻量级目录访问协议，基于 X.500 标准，但更简单，并可根据需要进行定制。mod_psldap 是 Apache 用来执行 LDAP 认证和授权的模块。同时可通过 Web 界面进行简单的 LDAP 管理

**9.带宽限制模块**

mod_cband 是一个用来限制请求占用带宽的 Apache 模块。

**10.CGI V8 引擎包**

v8cgi 是一个很小的 C ++ 和 JS 和 C 文件集合，允许开发者在服务器端使用 JS 的模块，基本功能包括：IO, GD, MySQL, Sockets, templates, FastCGI and Apache module.

## 配置文件(官网指令

[指令连接]: http://httpd.apache.org/docs/2.4/mod/directives.html

## )

```conf
Define SRVROOT "E:/phpstudy_pro/Extensions/Apache2.4.39" #定义变量
ServerRoot "${SRVROOT}" #服务器安装的基本目录

Define ENABLE_TLS13 "Yes" #定义变量

LoadModule access_compat_module modules/mod_access_compat.so #从ServerRoot的modules子目录加载命名模块



<IfModule unixd_module> # IfModule 标记以特定模块的存在为条件的指令,unixd_module Unix系列平台的基本（必需）安全性模块，
User daemon #用户标识
Group daemon #组标识
</IfModule>

ServerAdmin admin@example.com #服务器发送到客户端的错误消息中包含的电子邮件地址

ServerName localhost:80  #服务器用来标识自身的主机名和端口



<Directory /> #Directory>和</Directory>用于封装一组指令，这些指令将仅应用于命名目录、该目录的子目录以及相应目录中的文件。
    Options +Indexes +FollowSymLinks +ExecCGI #options 配置特定目录中可用的功能,Indexes 当网页不存在的时候允许索引显示目录中的文件,FollowSymLinks(默认选项) 服务器将遵循此目录中的符号链接 , ExecCGI 允许使用mod_cgi CGI执行CGI脚本。将带+或-的选项与不带+或-的选项混合在一起是无效的语法，在服务器启动期间将被带中止的语法检查拒绝。
    AllowOverride All #AllowOverride .htaccess文件中允许的指令类型,默认：AllowOverride None (2.3.9之后), AllowOverride All (2.3.8之前)
    Order allow,deny #Order 控制默认访问状态以及计算允许和拒绝的顺序,首先，所有允许的指令被评估；至少一个必须匹配，或者请求被拒绝。接下来，将计算所有Deny指令。如果有匹配项，则拒绝请求。最后，默认情况下，任何与Allow或Deny指令不匹配的请求都会被拒绝。。
    Allow from all #Allow 控制哪些主机可以访问服务器的某个区域.
    Require all granted #无条件允许访问。 Require:测试经过身份验证的用户是否由授权提供程序授权.
</Directory>

DocumentRoot "E:/phpstudy_pro/WWW" #DocumentRoot:apache的默认web站点目录路径

<Files ".ht*"> #Files:包含应用于匹配文件名的指令
    Require all denied
</Files>

ErrorLog "logs/error.log" #服务器记录错误的位置

LogLevel crit #控制错误日志的详细程度

Include conf/vhosts/*.conf #包括服务器配置文件中的其他配置文件

ErrorDocument 400 error/400.html #发生错误时服务器将返回给客户端的内容
KeepAliveTimeout 5 #服务器在持久连接上等待后续请求的时间量
MaxKeepAliveRequests 100 #持久连接上允许的请求数
Timeout 60 #服务器在请求失败之前等待特定事件的时间量
```


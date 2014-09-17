---
layout: post 
title: SPDY简介   
categories: 
- Network 
tags:   
- SPDY
---  

##SPDY是什么？

SPDY是Google开发的基于TCP，TLS的网络协议，用来传输web内容。HTTP2.0的第一个草案就是以SPDY为蓝本。
SPDY的目的是减少web页面加载时间和增进web安全。

<br>
##SPDY替换了HTTP吗?

没有。  
SPDY只是修改了HTTP 在网络上请求和响应的方式。这意味着现有的server端无需修改，只要有一个SPDY兼容的转换层在。
相对于HTTP，SPDY修改了连接管理(connection management)和数据传输格式（data transfer format）。

<br>
##设计架构。


![](http://www.cubrid.org/files/attach/images/220547/186/504/http_vs_spdy.png)

<br>
##功能

* 多路复用  

   SPDY 规定在一个 SPDY 连接内可以有无限个并行请求，即允许多个并发 HTTP 请求共用一个 TCP会话。

* 请求优先级

* HTTP头部压缩  

HTTP 1.1只支持response压缩，而SPDY对request，(request and response)header都支持压缩。

* 服务端推送

  总体来说，SPDY相对HTTP来说，会提升23%左右的性能，SPDY是一个domain对应一个连接，而HTTP会发起多个连接。

<br>
##谁在用SPDY
Chrome, Firefox, Opera, IE等都实现了SPDY。    
Google, Facebook, Twitter在其部分服务支持SPDY。  
Apache, Nginx都有对应的module支持SPDY。

要看看实际应用，可以访问google的服务，比如gmail, 图片，然后在浏览器输入

**chrome://net-internals/#events&q=type:SPDY_SESSION is:active**

<br>
## Twitter的SPDY框架

Twitter 开源了他们的 SPDY 框架 [CocoaSPDY（for iOS and OS X）][4]。  

SPDY**减少了大约30%的延迟（latency）**，参见：[CocoaSPDY: SPDY for iOS / OS X][6]

<br>
另外，推荐看看ihower的[《SPDY 简介》][5]

<br>
##Reference:
1 [SPDY][1]  
2 [What is SPDY? Deployment Recommendations][2]  
3 [SPDY The Chromium Projects][3]

<br>

微信公众账号：**iOS技术分享**  
![](http://farm3.staticflickr.com/2826/10855679484_56b7429bd6_m.jpg)


[1]:http://en.wikipedia.org/wiki/SPDY
[2]:http://www.cubrid.org/blog/dev-platform/what-is-spdy-deployment-recommendations/
[3]:http://www.chromium.org/spdy
[4]:https://github.com/twitter/CocoaSPDY
[5]:http://vdisk.weibo.com/s/nuU6W
[6]:https://blog.twitter.com/2013/cocoaspdy-spdy-for-ios-os-x





---
layout: post
title: "今晚数据库宕机带来的思考"
date: 2014-03-13 22:44:27 +0800
comments: true
categories: J2EE
---

>晚上刚刚到家一会，peter打电话说app取不到数据。最不愿发生的情况发生了，服务器宕机了。

<span>&nbsp;&nbsp;&nbsp;&nbsp;马上打开电脑先ping了下服务器，发现在线，ok！估计是数据库挂了。马上尝试连接了一下数据库，发现已经宕机。而且根本无法重启。硬重启了一下服务器后登录数据库服务器提示:</span>
<pre><code>
Can’t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock’ (2) 
</code></pre>

* 首先排除文件权限问题。ps:如果是权限问题可以尝试 #chown -R mysql:mysql /var/lib/mysql 

* 然后尝试重启mysql服务，未果。service mysqld start 后一直卡死。
 
* 查看了下配置文件，没有问题，排除。

&nbsp;&nbsp;&nbsp;&nbsp;就纳了闷了，问题出在哪呢？？上 [Stack Overflow ](http://stackoverflow.com/) 找类似问题的帖子看，发现大家的回答大多数都是围绕以上几个方面进行排雷，但我目前情况肯定是另外一种状况。
>突然间看到有个贴的回复说到造成这种原因有可能是磁盘满了

&nbsp;&nbsp;&nbsp;&nbsp;我随手 df -k看了下空间，Oh........ 原来不知不觉磁盘已经满了  :O 清理了下磁盘的垃圾数据和备份文件后，启动mysql服务，正常启动。

>教训和思考:遇到问题要冷静思考，全面分析产生问题的各种潜在隐患，然后逐一排除。有时一些问题的产生可能是由一些平时根本不在意的小细节引起的。


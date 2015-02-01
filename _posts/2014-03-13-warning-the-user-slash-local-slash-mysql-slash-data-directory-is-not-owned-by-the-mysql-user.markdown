---
layout: post
title: "Warning the user/local/mysql/data directory is not owned by the mysql user"
date: 2014-03-13 17:51:23 +0800
comments: true
categories: J2EE
---
>昨天安装MongoDB的时候手贱把mysql的db文件给删了，今天用mysql时发现无法连接到数据库，并且服务也启动不起来，提示‘the user/local/mysql/data directory is not owned by the mysql user’ 看提示应该是db文件新创建后的权限问题，用以下命令解决:

*  sudo chown -RL root:mysql /usr/local/mysql
*  sudo chown -RL mysql:mysql /usr/local/mysql/data
*  sudo /usr/local/mysql/support-files/mysql.server start
 
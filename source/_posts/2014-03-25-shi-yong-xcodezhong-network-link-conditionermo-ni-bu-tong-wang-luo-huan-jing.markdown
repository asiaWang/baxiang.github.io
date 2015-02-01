---
layout: post
title: "使用Xcode中Network Link Conditioner模拟不同网络环境"
date: 2014-03-25 10:36:41 +0800
comments: true
categories: ios
---
>太久不做ios的后果就是很少接触到相关的技巧和信息 :(   早上看到群里@fish分享的一个小工具可以让用户模拟不同的网络连接和带宽，可供Mac和iOS开发者测试自己的程序在不同网络环境下的表现。 

####1.进入Apple开发者下载中心，Network Link Conditioner包含在Hardware IO Tools工具包中，点击下载。

![Apple开发者下载中心](http://ww4.sinaimg.cn/large/775c483ftw1eert21mbowj20v70esn0n.jpg)

####2.下载安装后，可看到其中有一个Network Link Conditioner.prefPane文件。
![](http://dl2.iteye.com/upload/attachment/0080/1671/be215461-fe8f-389a-8d2c-481e6be71dd5.png)

####3. 点击运行Network Link Conditioner.prefPane后，Network Link Conditioner就会被添加到系统偏好设置的其他分类中。

![](http://dl2.iteye.com/upload/attachment/0080/1677/9b40d84e-ce6f-3f32-af20-27f2a471dc1a.png)

> 启动Network Link Conditioner就可以使用iOS模拟器测试APP在此种环境下的运行情况了。在测试完毕时，记得停止Network Link Conditioner，Network Link Conditioners是对整个系统有效的，普通上网的速度也会被限制

####4.Network Link Conditioner下载链接: [http://adcdownload.apple.com/Developer_Tools/hardware_io_tools_for_xcode__february_2012/hardware_io_tools_for_xcode.dmg](http://adcdownload.apple.com/Developer_Tools/hardware_io_tools_for_xcode__february_2012/hardware_io_tools_for_xcode.dmg)  
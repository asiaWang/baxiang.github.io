---
layout: post  
title: iOS应用程序安全(13)-使用Sogeti Data Protection tools启动定制的Ramdisk  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-13-booting-a-custom-ramdisk-using-sogeti-data-protection-tools/  
版权声明：自由转载-非商用-保持署名


在前面的文章当中，我们学习了如何使用Keychain-Dumper和Snoop-it来从iOS设备上导出和分析keychain的内容。本文我们将看看如何用一个自己的ramdisk来启动一个没有越狱的设备，并且分析该设备的内容。

为什么要用一个定制的ramdisk来启动设备呢？假设一个场景，你只能短暂的访问一个设备，并且不能越狱它。你只能访问这个设备，比如：30分钟。
这种情况下，你可以用定制的ramdisk启动设备，然后暴力破解口令，导出所有数据以便后续分析。为了执行这样的攻击，最好的情况就是这个设备不需要越狱。
当然，如果设备使用字母数字组合的密码，那可能就需要更长的时间来暴力破解口令了。你可以把这种情况设想为用一个Linux live CD来启动一个windows机器，
然后加载windows分区，然后使用Linux OS来访问硬盘的内容。不过，使用定制的ramdisk来加载设备需要一个bootrom漏洞。[bootrom][1]是IDevice上运行的第一个重要的代码。
bootrom漏洞允许我们在bootloader的低层次绕过bootrom签名验证，使得我们可以用定制的ramdisk启动设备。这样的漏洞能够让用户执行未签名的代码，因此可以创造一个
untethered jailbreak。能够获得bottom漏洞列表在[此][2]。一个bottom漏洞一旦被发现就不可能被苹果以发布一个新的iOS系统的方式来修复，只可能发布新硬件才能修复。
在写本文的时候，从采用A5及以后（芯片）的设备，没有发现bootrom exploit。本文我们将要使用的bootrom利用将只能工作在使用A4的设备上。我将使用一个4代iPod touch，
因为它有一个A4芯片。本文我们将专注在如何用定制的ramdisk来启动设备，我们将在下一篇文章中关注如何利用启动后的设备。

一个非常容易的方法就是下载[这个工具][3]，这个工具允许自动的SSH ramdisk生成。它非常易用，你可以看看这个[视频][4]了解更多细节。不过，在本文当中，我们将会看看
Sogeti data protection tools，最主要的原因就是它提供了许多我们可以利用的python脚本。


关于如何使用定制的ramdisk来启动的指令可以看[这][5]。我们将在运行Mac OSX 10.8.4的系统上运行同样的指令。

首先，确保你的系统上安装了Mercurial。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/082213_1252_IOSApplicat1.png) 


然后，创建一个新目录，并进入到里面。然后安装ldid工具，请确保它是可执行的并把它移动到/usr/bin目录。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/082213_1252_IOSApplicat2.png) 

我们需要创建些符号连接。请注意下图的第二个命令，我们创建了一个到Xcode.app的符号连接。通常情况下如果你是iOS开发者，并且在系统上安装了多个Xcode版本，
你可能已经把它们命名为不同的名字，比如Xcode-4.5.2或者Xcode-5.1。因此，请注意这个命令可能执行失败，并提示你iOS SDK找不到。一个解决方法就是
把你的Xcode应用的文件夹命名为Xcode。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/082213_1252_IOSApplicat3.png)

下一步，我们需要安装OSXFuse，可以用来为OS X创建文件系统。根据你运行的操作系统而定，有些版本的OSXFuse可能会不兼容。因此，请确保下载能在你的系统上运行的
版本。我下载的是版本是2.5.4，在OS X 10.8.4上工作良好。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/082213_1252_IOSApplicat4.png) 


![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat5.png)

然后你需要安装一些python模块

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/082213_1252_IOSApplicat6.png) 

下一步就是构建定制的ramdisk和内核。我们需要先clone iphone data protection。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat7.png)

然后我们使用下面的命令来创建文件系统。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat8.png) 

然后我们就可以下载redsn0w, 拷贝其中的Keys.plist文件

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat9.png)

现在你需要下载任意版本的iOS5的IPSW文件。与目标设备上目前正在运行什么操作系统没有关系。你可以根据你的设备从[这][6]下载IPSW文件。
一旦IPSW文件下载完毕，把它复制到当前目录。然后用下面的命令给内核打补丁，参数就传刚刚下载的IPSW文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat10.png) 


这样就会创建好一个.sh文件，我们可以用它来构建ramdisk。我们运行看看。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat11.png) 


一旦ramdisk创建好了，你会在命令行看到你要启动这个ramdisk所需要的命令。


![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat12.png) 


现在我们使用这个命令来启动定制的ramdisk然后给内核打补丁。请注意即使这个命令以redsn0w起头，我们已经移到redsn0w所在的目录并以该目录下的
二进制文件替换掉命令中的redsn0w。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat13.png) 

这样就会打开redsn0w.

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat14.png) 

现在我的iPod正连接到设备，并且没有关机。因此我们按redsn0w的要求关机。一旦iPod关机，redsn0w上的下一步按钮就可以点击（enable）了。点击下一步，
然后你会看到redsn0w要求你让设备进入DFU模式。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat15.png)  

我们依照redsn0w的要求操作。一旦你的设备进入DFU模式，你会看到redsn0w已经开始注入漏洞利用程序。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat16.png)  
 
然后它会重启设备。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat17.png)  
 
你的设备看起来会像下面的图这样。一段时间之后，你会在屏幕之上看到一个大大的ok说明漏洞利用程序已经成功执行并且这个定制的ramdisk也已经开启了。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat19.jpg)   

data protection tools中也包含一个python实用工具叫做 usbmux，现在我们可以用它通过ssh来访问设备。让我们用python脚本 tcprelay 先建立中继转发。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat20.png)   

在一个不同的tab上，我们能够ssh进本地的2222端口，然后usbmux会把这个连接中继转发（forward）到通过USB连接到端口22的远程设备上。请注意你的设备需要
通过USB连接到你的电脑上。然后输入ls。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat21.png)   

我们可以看到一个mount_partitions shell脚本。我们可以用它来加载分区。然后我们回到root目录，我们可以看到2个分区，mnt1和mnt2。我们可以进入并探索这个文件系统了。

![](http://resources.infosecinstitute.com/wp-content/uploads/082213_1252_IOSApplicat22.png)    
 

恭喜你！我们已经成功的用一个定制的ramdisk启动设备了，我们现在能够进一步探索这个文件系统了。

<br/>
## 总结

本文我们学习了如何通过定制的ramdisk启动iOS设备，并且可以用它来进一步探索该设备的文件系统。在接下来的文章中，我们将看看Sogeti data protection tools 提供的不同工具，然后用这些工具做不同的任务，比如暴力破解口令，导出keychain文件，甚至对整个文件系统做备份。

References

Iphone-dataprotection – IOS Forensics tools
https://code.google.com/p/iphone-dataprotection/

<br/>
本文原文是 [IOS Application Security Part 13 – Booting a custom Ramdisk using Sogeti Data Protection tools][7]



[1]:http://theiphonewiki.com/wiki/Bootrom 
[2]:http://theiphonewiki.com/wiki/Category:Bootrom_Exploits
[3]:http://msftguy.blogspot.in/2012/01/automatic-ssh-ramdisk-creation-and.html
[4]:http://www.youtube.com/watch?feature=player_embedded&v=1dh5loiX1dU
[5]:https://code.google.com/p/iphone-dataprotection/wiki/README
[6]:http://theiphonewiki.com/wiki/Firmware
[7]:http://resources.infosecinstitute.com/ios-application-security-part-13-booting-a-custom-ramdisk-using-sogeti-data-protection-tools/




















































































---
layout: post  
title: iOS应用程序安全(14)-使用Sogeti Data Protection tools收集信息  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-14-gathering-information-using-sogeti-data-protection-tools/  
版权声明：自由转载-非商用-保持署名


在前面一篇文章中，我们看到了如何使用Sogeti Data Protection tools用定制的ramdisk启动iOS设备。本文我们将如何用其中的一些工具来收集设备的信息，比如keychain的内容，导出整个文件系统甚至暴力破解口令。

在上一篇文章当中，我们已经成功的用usbmux与设备建立了连接，ssh进设备，并且加载了分区。下面是分区包含的内容。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat1.png)

现在我们可以自由的在这些分区内部切换，做任何我们想要做的事情。正如我们看到的，iphone data protection tools给我们提供了一些可以使用的脚本。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat2.png)

现在让我们使用下暴力破解脚本。当前我这个ipod的口令（passcode）是“1234”。这意味着keychain以这个口令保护着（参见前面的文章），因此如果我们
想要到处keychain中的数据，我们就需要找到口令。我们先运行这个脚本。如你所见，它开始执行暴力破解。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat3.png) 

一段时间之后，我们可以看到它成功的找到我们的口令。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat4.png) 

可以看到，这些结果被写入到一个plist文件。这个plist文件被其他python脚本使用，例如，被口令保护的keychain信息导出的时候需要使用口令。这里还有另一个python
脚本可以用来暴力破解口令，位于data protection tools的python_scripts目录下。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat5.png)


一旦口令被算出，它就会以你的设备的UDID为名创建一个文件夹，然后把包含口令信息的plist文件保存到这个文件夹中。你可以在其他时候使用这个plist文件，
比如解密keychain的时候。


![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat6.png) 


下面就是那个plist文件保存的信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat7.png)  

请注意，使用一个字母、数字组合的密码会让这个破解更困难，因此使得keychain的数据得到更好的保护。不过，大多数人都不关注这个，并且喜欢使用
简单的4位数字密码。

不管怎样，先回到设备上，这里有另一个脚本叫做device_infos可以用来导出关于设备的信息，比如序列号，Mac地址等等。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat8.png) 


其中最重要的一个python脚本之一就是ios_examiner python脚本。不过，我在运行的时候，得到一个关于unicode字符串的错误提示，我在[这个链接][1]的帮助下修改了这个
python脚本。
 
现在运行ios_examiner 这个python脚本。你会看到一个可以输入命令的解释器。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat9.png)  




你可以通过输入help来看到所有的命令集合。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat10.png)   



你可以输入keychain来导出keychain。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat11.png)   


不过，你会注意到输出说“ Keybag state: locked”，因此有些条目没有显示（显示为？）。有些条目被更高的保护属性保护（比如AfterFirtUnlock)，
因此它们需要设备的口令才能解密。不过，我们前面不是已经成功的找到了口令了呀？是的，虽然已经找出了口令，不过我们用了另一个python
脚本，因此保持口令信息的plist文件被保存在另外的地方。最简单的方法就是在解释器中使用暴力破解脚本，输入口令，让它保存plist文件到当前
位置，现在你可以再次使用keychain脚本了。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat12.png)

使用命令protected_files会给出一系列不是以NSProtectionNone保护的文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat13.png) 


你也可以导出当前的分区为一个.dmg文件供后续加载分析使用。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat14.png) 


还有另一个shell脚本可以为你导出分区。它把创建的.dmg文件保存到一个以设备的UDID命名的文件夹内。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat15.png) 

一旦这个.dmg文件生成完毕，你可以选择它并加载。现在你可以在任意时候探索这个文件系统了 。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat16.png) 


现在你可以使用reboot命令来重启系统，然后系统就会回到之前的状态。请确保设备与你的电脑断开之后再这么做（重启系统）。

![](http://resources.infosecinstitute.com/wp-content/uploads/082713_1245_IOSApplicat17.png)  


<br/>
## 总结

本文我们学习了使用Sogeti Data Protection tools 来从设备上收集不同的信息，比如keychain数据，被保护的文件，等等。我们也可以导出整个文件系统以便随后再分析。
这个技巧可以在任何使用A4芯片的设备上用bootrom 漏洞利用使用，而且并不需要对设备越狱，设备重启之后又会回到正常状态，因此，普通用户根本不知道发生了什么。


References

 Iphone-dataprotection – IOS Forensics tools
https://code.google.com/p/iphone-dataprotection/


<br/>
本文原文是 [IOS Application Security Part 14 – Gathering information using Sogeti Data Protection tools][2]

[1]:https://code.google.com/p/iphone-dataprotection/issues/detail?id=70
[2]:http://resources.infosecinstitute.com/ios-application-security-part-14-gathering-information-using-sogeti-data-protection-tools/














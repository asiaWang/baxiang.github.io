---
layout: post  
title: iOS应用程序安全简要总结  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：吴发伟  
版权声明：自由转载-非商用-保持署名

前面介绍了iOS应用安全系列文章，文章列表如下:

[iOS应用程序安全(1)-搭建移动渗透测试平台][1]  
[iOS应用程序安全(2)-获得iOS应用程序的类信息][2]  
[iOS应用程序安全(3)-理解Objective-C Runtime][3]  
[iOS应用程序安全(4)-用Cycript进行运行时分析(Yahoo天气应用)][4]  
[iOS应用程序安全(5)-用Cycript做运行时分析的高级技巧(Yahoo天气应用)][5]  
[iOS应用程序安全(6)-iOS 7的新安全功能][6]  
[iOS应用程序安全(7)-不用证书安装应用到设备的方法][7]  
[iOS应用程序安全(8)-用Cycript进行Method Swizzling][8]  
[iOS应用程序安全(9)-用Snoop-it分析iOS应用的安全][9]  
[iOS应用程序安全(10)-iOS文件系统和取证][10]  
[iOS应用程序安全(11)-分析使用HTTP/HTTPS的网络流量][11]  
[iOS应用程序安全(12)-导出Keychain数据][12]  
[iOS应用程序安全(13)-使用Sogeti Data Protection tools启动定制的Ramdisk][13]  
[iOS应用程序安全(14)-使用Sogeti Data Protection tools收集信息][14]  
[iOS应用程序安全(15)-使用iNalyzer对iOS应用进行静态分析][15]  
[iOS应用程序安全(16)-使用iNalyzer对iOS应用进行动态分析][16]  
[iOS应用程序安全(17)-使用Introspy对iOS应用进行黑盒测试][17]  
[iOS应用程序安全(18)-使用Introspy检测自定义签名][18]  
[iOS应用程序安全(19)-在程序中使用Introspy][19]  
[iOS应用程序安全(20)-本地数据存储及其安全性（NSUserDefaults, CoreData, Sqlite, Plist 文件）][20]  
[iOS应用程序安全(21)-ARM和GDB基础][21]  
[iOS应用程序安全(22)-使用GDB进行运行时分析和操作][22]  
[iOS应用程序安全(23)-对抗运行时分析和操作][31]  
[iOS应用程序安全(24)-对抗运行时分析和操作][32]  
[iOS应用程序安全(25)-对抗运行时分析和操作][33]  
[iOS应用程序安全(26)- 使用IDA Pro给iOS应用打补丁][34]

 <br/>
相信读者把这一系列文章都看一遍，会对iOS应用的安全有个较为深入的了解。   

在[通过SSL Pining提供iOS SSL通信的安全][23]，介绍了使用SSL通信需要注意的问题。  

[Keychain is not safe][24]
介绍了即使使用keychain保存数据，也不应该保存明文密码。

[iOS Application Security][25]简要介绍了下iOS应用安全，简要讨论了关键数据的保存，关键数据是需要加密的。Encrytion is a must for sensitive data。
也简要讨论了通信的安全，http请求应该要做到能够防篡改、防重放攻击（replay attack）等等，如果安全要求更高，还要用https， 在客户端用https，[也要注意对源的校验][23]。使用http的时候，不要把用户名和密码直接通过网络请求发送到server，一定要有安全方面的考虑，即使使用HTTPS，也最好不要用明文传递密码，还要考虑中间人攻击（Man in the middle）的情况。不过，正如在[iMessage Privacy][26]看到的那样，安全之路，任重道远。

 <br/>
[iOS越狱程序开发（1）－ 工具篇][27]  
[iOS越狱程序开发（2）－ 构建和部署][28]  
[iOS越狱程序开发（3）－ Your First Tweak][29]  
[iOS越狱程序开发（4）－ 总结][30]  

这4篇文章，简要介绍了如何进行iOS越狱程序开发，可以在越狱设备上做很多有趣的事情，**能做的事情，有时候仅仅受限于你的想象力**。写这4篇文章的时候，我用这几篇文章介绍的相关技术实现些demo，能够在越狱设备上在运行时截取某些知名应用的敏感信息，并能把数据发送到server，你从这4篇文章就能学到相关的技巧。


把这些写出来的目的是知己知彼。开发者要了解攻击者能做什么，只有我们了解攻击者能做什么，怎么做到的，才能更好的确保我们应用的数据安全，我们的通信安全和用户敏感数据的防护，有时候，安全也可能成为你的竞争力。


谨以这些文章抛砖引玉，仓促而就，文中谬误之处，请大家多多指点并留言，一定修正。谢谢。


<br>
微信公众账号：**iOS技术分享**  
![](http://farm3.staticflickr.com/2826/10855679484_56b7429bd6_m.jpg)  




[1]:http://wufawei.com/2013/11/ios-application-security-1/
[2]:http://wufawei.com/2013/11/ios-application-security-2/
[3]:http://wufawei.com/2013/11/ios-application-security-3/
[4]:http://wufawei.com/2013/11/ios-application-security-4/
[5]:http://wufawei.com/2013/11/ios-application-security-5/
[6]:http://wufawei.com/2013/11/ios-application-security-6/
[7]:http://wufawei.com/2013/11/ios-application-security-7/
[8]:http://wufawei.com/2013/11/ios-application-security-8/
[9]:http://wufawei.com/2013/11/ios-application-security-9/
[10]:http://wufawei.com/2013/11/ios-application-security-10/
[11]:http://wufawei.com/2013/11/ios-application-security-11/
[12]:http://wufawei.com/2013/11/ios-application-security-12/
[13]:http://wufawei.com/2013/11/ios-application-security-13/
[14]:http://wufawei.com/2013/11/ios-application-security-14/
[15]:http://wufawei.com/2013/11/ios-application-security-15/
[16]:http://wufawei.com/2013/11/ios-application-security-16/
[17]:http://wufawei.com/2013/11/ios-application-security-17/
[18]:http://wufawei.com/2013/11/ios-application-security-18/
[19]:http://wufawei.com/2013/11/ios-application-security-19/
[20]:http://wufawei.com/2013/11/ios-application-security-20/
[21]:http://wufawei.com/2013/11/ios-application-security-21/
[22]:http://wufawei.com/2013/11/ios-application-security-22/
[23]:http://wufawei.com/2013/05/SSL-Pining/
[24]:http://wufawei.com/2013/06/Keychain-is-not-safe/
[25]:http://wufawei.com/2013/07/iOS-application-security/
[26]:http://wufawei.com/2013/10/iMessage-Privacy/
[27]:http://wufawei.com/2013/08/iOS-jailbroken-programming-1/
[28]:http://wufawei.com/2013/08/iOS-jailbroken-programming-2/
[29]:http://wufawei.com/2013/08/iOS-jailbroken-programming-3/
[30]:http://wufawei.com/2013/08/iOS-jailbroken-programming-4/
[31]:http://wufawei.com/2013/11/ios-application-security-23/
[32]:http://wufawei.com/2013/11/ios-application-security-24/
[33]:http://wufawei.com/2013/11/ios-application-security-25/
[34]:http://wufawei.com/2013/12/ios-application-security-26/
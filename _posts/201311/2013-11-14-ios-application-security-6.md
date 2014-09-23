---
layout: post  
title: iOS应用程序安全(6)-iOS 7的新安全功能  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-6-new-security-features-in-ios-7/  
版权声明：自由转载-非商用-保持署名

##引言

我们都知道，苹果在WWDC 2013介绍了新的iOS系统,有着完全重新设计的用户界面。如果你还没看过，请看看WWDC 2013的视频。

与本系列的其他文章相比，我们在本文将稍微偏离下主题。在本文中，我们将介绍在iOS7中引入的新安全功能。

<br>
## 获得 iOS7

为了获得iOS 7 beta，你需要是一名注册的苹果开发者。到http://developer.apple.com，用你注册的苹果id登陆，然后跳转到[这个地址][1]。然后点击并下载iOS 7 SDK。这样你就可以下载最新的iOS 7 beta版本了。

![](http://resources.infosecinstitute.com/wp-content/uploads/070113_1824_IOSApplicat1.png)



下面是一些随着iOS 7一起介绍的安全功能

<br>
##激活锁

在以前，如果用户丢失了他们的iPhone，他们可用“查找我的iPhone”，用GPS来跟踪他们的手机。不过这个流程有个非常大的问题。即使是一个不那么警觉的小偷，也会对设备执行恢复出厂设置，这样所有的内容和设置都会被擦除。然后，有了新的激活锁这个功能，要执行恢复出厂设置，就需要知道用户的苹果id对应的密码。

要验证这个功能，请到设置，然后是通用，滚动到底部，你可以看到一个重置按钮。


![](http://resources.infosecinstitute.com/wp-content/uploads/070113_1824_IOSApplicat2.png)


如果你点击这个按钮，然后点击擦除所有内容和设置，你会看到一个弹出框，要求你输入苹果id对应的密码。

![](http://resources.infosecinstitute.com/wp-content/uploads/070113_1824_IOSApplicat3.png)

这是一个非常方便灵活的功能，可以防止小偷重置你的iPhone的内容和设置。不过我们确定当这个公布以后，迟早会有人找到绕过这个功能的方法。你如果想去iCloud的设置关闭查找我的iPhone，也会得到同样的弹出框。

![](http://resources.infosecinstitute.com/wp-content/uploads/070113_1824_IOSApplicat4.png)

正如我们看到的，要求我们输入苹果id对应的密码。

![](http://resources.infosecinstitute.com/wp-content/uploads/070113_1824_IOSApplicat5.png)

<br>
##Airdrop传输的数据加密

Airdrop使得你能够简单点击几下就能与周围人分享文件。Airdrop是iOS 7中引入的。好消息是，这些传输的文件都是加密过的。默认这项功能只对你的联系人可见。不过你也可以更改这个设置。

![](http://resources.infosecinstitute.com/wp-content/uploads/070113_1824_IOSApplicat6.png)


<br>
##第三方应用数据保护

数据保护依赖于支持硬件加密的设备，支持的设备从iPhone 3Gs开始。根据苹果的[官方文档][2]，数据保护通过用你的密码来保护硬件加密秘钥，增强了内建的硬件保护。对邮件地址和附件来说，提供了另一层的保护。从iOS 4开始，第3方应用就可以使用数据保护APIs来保护应用的数据。

在iOS 6，你可以到设置-通用-密码来选择一个密码，你可以看到底部有个文本，说数据保护已经启用。这意味着苹果在iOS6中默认对它自己的应用开启了数据保护这个功能。

![](http://resources.infosecinstitute.com/wp-content/uploads/070113_1824_IOSApplicat7.png)

在以前，开发者不得不使用数据保护API来保护应用数据。现在，在iOS7中，所有的第3方应用都自动开启了数据保护，这意味着用户解锁设备之前，所有应用内部的数据都是安全的。 我们知道，默认的，苹果允许4位数字密码，这个很容易被暴力破解。因此，为了安全，推荐使用更安全的密码。

<br>
##游戏中心的安全改进

如果你经常在iOS上玩游戏，并且经常用游戏中心来与朋友竞争或者查看分数，你可能会看到某些特定游戏的不同寻常的高分数。通常情况下，这些都不是合法的分数，这些是黑客利用API的漏洞或者在运行时修改应用的变量才有高分数。在iOS 7中，苹果现在允许开发者确保这种破坏数据的情况不再发生。现在，开发者可以为每个游戏设置一个最高分数。这样，有着不寻常高分数的人，其数据会被上传保存到服务器，却不会显示在排行榜上。开发者随后可以决定是否让这些不同寻常的高分显示。苹果同时也为开发者增加了工具，使得开发者能够检查用户的可疑行为，在这种情况下，可以封锁这个用户。苹果也集成了签名过的游戏中心提交，这意味着苹果能够拒绝这些分数，如果苹果确信这些分数被别人修改过。不确定苹果是怎么做到这点的。

<br>
##iCloud Keychain
大部人可能都使用过safari的自动填写功能。在iOS7上，Safari现在可以记住更多信息，例如用户名，密码，信用卡号等等，并且把这些信息保存在keychain中。然后它就会在用户访问网页的时候自动填写。所有这些信息都是用256位的AES加密算法加密了的。这个keychain也会通过iCloud同步。为每一个你登录的站点都创建一个新密码会有点烦人，并且难以记忆。Safari内建了一个密码生成器，为你登录的每个不同站点创建不同的密码，你自己都不需要去记它。

<br>
##Per App VPN

有时候，用户需要通过一个安全通道取访问机密信息，因此他们使用VPN。在iOS 7中，苹果允许为不同应用使用不同的VPN。这非常有用，比如你想用公司的app通过公司提供的VPN来访问公司数据，而其他应用，你可能想要用其他VPN。这个功能对政府雇员也很有用，这样他们就可以通过手机应用来访问机密数据。

如果你是一个iOS 开发者并且想要学习iOS引入的新功能，你可以在[这][3]观看WWDC 2013的视频。

<br/>
References:
[Apple sticks it to cheaters with Game Center on iOS 7][4]

<br/>
本文原文是 [IOS Application Security Part 6 – New Security Features in IOS 7][5]

[1]:https://developer.apple.com/devcenter/ios/index.action
[2]:http://support.apple.com/kb/ht4175
[3]:https://developer.apple.com/wwdc/videos/
[4]:http://www.idownloadblog.com/2013/06/15/ios7-game-center-dev-tools/
[5]:http://resources.infosecinstitute.com/ios-application-security-part-6-new-security-features-in-ios-7/

---
layout: post 
title: 使用Reveal来查看、修改、调试iOS应用  
categories: 
- iOS 
tags:   
- iOS
---  


之前在[Quick Links][1]中提到过，这里再简要介绍下[Reveal][3]。

> 它能够在运行时调试和修改iOS应用程序。Reveal能连接到应用程序，并允许开发者编辑各种用户界面参数，这反过来会立即反应在程序的UI上。就像用FireBug调试HTML页面一样，在不需要重写代码、重新构建和重新部署应用程序的情况下就能够调试和修改iOS用户界面。  --[InfoQ][2]

在[Reveal][3]主页上有对其功能的介绍和演示，之前是beta版本，可以免费下载。现在正式版本可以下载试用一段时间，试用期后需要购买。有需要的话可以买一个，功能相当强大。
<br>
###加载方法（1）  

下载Reveal之后打开，在菜单中的Help中可以找到集成到Xcode项目的方法，这里不再赘述。
<br>
###加载方法（2）  

不过上述方法略显繁琐。  
[Integrating Reveal without modifying your Xcode project][4]   
[reveal: 檢視 iOS app 的 view 結構][5]。  
给出了如何不用修改Xcode工程就可以加载使用Reveal的方法。
<br>
###加载方法（3）  

[Reveal查看任意app的高级技巧][7]介绍了如何在越狱设备上查看任意app的技巧：


**将libReveal.dylib上传到设备的/Library/MobileSubstrate/DynamicLibraries**。  

**编辑并上传一个libReveal.plist**。

更详细的步骤，请参见原文。


最后，可以看看关于Reveal的tips:[Reveal tips: Navigation][6]。


<br>

[iOS 7已经可以越狱了][8]，**建议最好还是不要越狱**。 越狱设备上有许多安全问题，具体可以参看我之前的博客文章。



<br>

微信公众账号：**iOS技术分享**  
![](http://farm3.staticflickr.com/2826/10855679484_56b7429bd6_m.jpg)

[1]:http://wufawei.com/2013/11/quick-links/ 
[2]:http://www.infoq.com/cn/news/2013/07/debug_ios_apps_with_reveal 
[3]:http://revealapp.com/
[4]:http://blog.ittybittyapps.com/blog/2013/11/07/integrating-reveal-without-modifying-your-xcode-project/
[5]:http://adison.logdown.com/posts/159350-reveal-check-the-ios-app-view-structure
[6]:http://blog.ittybittyapps.com/blog/2013/09/26/reveal-shortcuts-and-tips/
[7]:http://c.blog.sina.com.cn/profile.php?blogid=cb8a22ea89000gtw
[8]:http://evasi0n.com/








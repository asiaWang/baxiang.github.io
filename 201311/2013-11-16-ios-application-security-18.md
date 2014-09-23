---
layout: post  
title: iOS应用程序安全(18)-使用Introspy检测自定义签名  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-18-detecting-custom-signatures-with-introspy/  
版权声明：自由转载-非商用-保持署名


在前面的文章中，我们查看了如何使用Introspy对iOS应用进行黑盒评估。本文我们将看看如何使用Introspy来建立自定义的签名，然后在追踪应用的时候检测这些自定义签名。

如果你对某个特定应用的某个方法特别有兴趣，并且你想直到它什么时候被调用，那么，建立预定义的签名可能会非常有用。Introsp已经有一些预定义的签名用来检测漏洞和不安全的配置。不过，它也允许我们添加自己的签名。


你可以在分析器的文件夹里面的signatures.py找到预定义的签名。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat1.png)

从这里，我们可以看到一个签名由标题，描述，严重等级，以及一个由方法调用组成的过滤器。让我们看看例子签名。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat2.png)


在上面的图中，你可以看到这个签名检查应用是否使用了剪贴板（Pasteboards）。剪贴板不太安全，因为他们允许应用从其中拷贝信息。
因此这个签名就说得通了。你可以看到这个过滤器（filter）有2个值，一个是classes_to_match，一个是methods_to_match。你也可以在
签名中指定一个参数args_to_match。从这个例子签名中，可以看到如下面的方法实现会符合上面的签名。


	* UIPasteboard *pasteboard = [UIPasteboard generalPasteboard];

	* UIPasteboard *pasteboard = [UIPasteboard pasteboardWithName:@"XYZ" create:YES];

	* UIPasteboard *pasteboard = [UIPasteboard pasteboardWithUniqueName];


下图所示的另一个签名检查在连接远程服务器的时候绕过凭证验证。这会发生在你使用一个自签名的SSL证书，然后想信任它而不必进行
任何验证的时候。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat3.png)




如果任意的LibC签名，让我们把classed_to_match设置为"C"


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat4.png)


现在，我们看看有参数并把参数也被当作过滤器的签名。这个过滤器能使用3个类来定义，这3各类可以在文件Filters.py中找到。这些类是
 ArgumentsFilter, ArgumentsNotSetFilter 和 ArgumentsWithMaskFilter。下面是从这些代码中用来表示其用途的截图。



![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat5.png)

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat6.png)

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat7.png)


下面是Signatures.py中的一个签名，用来检测当有数据没有secure protection domain(pdmn)又被写入keychain的时候。如你所见，

ArgumentsFilter 和 ArgumentsNotSetFilter 过滤器已经被使用来检测签名。ArgumentsFilter 用来找出pdmn不安全的情况。而ArgumentsNotSetFilter 
用来找出没有提供accessibility 选型而默认是kSecAttrAccessibleAlways的情况。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat8.png)

现在，让我们往signature.py文件中添加一个自定义签名。在这里，我们将要检测任何取NSUserDefaults中字符串的场景。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat9.png)

现在运行introspy.py这个python脚本，参数是保存的数据库。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat10.png)

在报告的潜在发现里面（Potential Findings），你会看到这个签名在许多不同的地方都被识别出来了。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/092413_1140_IOSApplicat11.png)


<br>
##总结


本文我们查看了如何使用Introspy来建立自定义的签名，然后在追踪应用的时候检测这些
自定义签名。在对这些应用进行静态分析的时候，如果你想追踪特定的方法实现，那么使用这些自定义签名会非常有用。   




References

 Introspy

https://github.com/iSECPartners/introspy



 <br/>
本文原文是 [IOS Application Security Part 18 – Detecting custom signatures with Introspy][1]

[1]:http://resources.infosecinstitute.com/ios-application-security-part-18-detecting-custom-signatures-with-introspy/

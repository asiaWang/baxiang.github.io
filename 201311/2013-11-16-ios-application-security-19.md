---
layout: post  
title: iOS应用程序安全(19)-在程序中使用Introspy  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-19-programmatical-usage-of-introspy/  
版权声明：自由转载-非商用-保持署名


在本文当中，我们将看看如何在我们自己的脚本中使用Introspy这个模块。


第一件事情就是引入Introspy模块，然后从argparse模块中引入Namespace。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat1.png)

然后我们创建Introspy类的实例。我们需要提供数据库名、组名、子组名和列表作为参数。在这里，除了数据库地址之外，我们为其他所有参数提供None。
因此，Introspy会包含所有的组而不是仅包含某个特定的组。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat2.png)

现在让我们创建一个带有组(group)和子组(subgroup)的对象。但是，我在文档中找不到组和子组的不同类目，不过从我们在前面文章中Introspy
生成的HTML报告中，我能找到组和子组的列表。看下图，所有的主要菜单项都可以用来作为组名称，而所有的子菜单项都可以用来作子组名称。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat3.png)


所以，例如，组名可以为DataStorage，Crypto, Network，IPC等等，Network的子组名可以为HTTP。现在让我们创建一个Introspy对象，组为Netwrok,
子组为HTTP。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat4.png)

现在看看这个introspy对象的所有方法和属性。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat5.png)

如我们所见，这个Introspy对象有一个analyzer属性。我们看看analyer属性的所有方法和属性。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat6.png)


可以看到，所有的发现（findings）要么使用finding 属性要么使用get_finding方法。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat7.png)

我们也可以通过signatures这个属性来查看analyzer的所有签名。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat8.png)

不过，这些都是签名对象的实例。让我们从这些签名上获得些有意义的信息。如果我们看看signatures.py文件，我们会看到每个Signatures实例都有
属性：标题名，描述，过滤器等等。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat9.png)

现在，让我们迭代每一个签名队形，然后打印其标题名和描述

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat10.png)


如你所见，我们能够从这些实例中找出许多我们想要的信息。在接下来的例子里，我将会打印出每个签名的所有类和方法的组合。
请注意，这可能并不是filter所使用的方法实现，因为这个方法可能是类方法，也可能是实例方法。例如，如下面输出可见，这里并没有方法叫做
[NSUserDefaults stringForKey:]  不过，有个实例方法是,  [[NSUserDefaults standardUserDefaults] stringForKey:]

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat11.png)

每个analyzer对象都有一个tracedCalls属性，它是在运行时追踪到的方法调用列表。如果我们看看tracedCalls类的代码，我们可以看到它有callId，method,clazz等等
属性。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat12.png)

我们可以从任意的tracedCall分析我们想要的任何信息。如下图所示，我打印出了第一个被追踪的调用的属性。

![](http://resources.infosecinstitute.com/wp-content/uploads/093013_1617_IOSApplicat13.png)


<br/>
##总结
在本文当中，我们查看了如何在我们自己的脚本中使用Introspy这个模块，然后用它从生成的数据库文件中获取签名，tracedCalls等信息，然后用我们想要的方式展示出来。这可以用在对某个特定应用准备我们自己的分析报告的时候。这些信息也同样可以传入其他python脚本以便做进一步的工作，比如进行运行时操作等。




References

Introspy

https://github.com/iSECPartners/introspy


 <br/>
本文原文 [IOS Application Security Part 19 – Programmatical Usage of Introspy][1]





[1]:http://resources.infosecinstitute.com/ios-application-security-part-19-programmatical-usage-of-introspy/






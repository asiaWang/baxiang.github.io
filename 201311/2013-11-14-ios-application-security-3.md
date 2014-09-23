---
layout: post  
title: iOS应用程序安全(3)-理解Objective-C Runtime   
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-3-understanding-the-objective-c-runtime/  
版权声明：自由转载-非商用-保持署名

### 引言


基本上所有的iOS应用都是用Objective-C编写的。所有这些应用都使用Cocoa，Cocoa在Objective-C上面，提供更高层次的API，使得Mac和iOS开发变得更容易一些。Cocoa也给应用提供了一个运行时环境。本文我们将着重理解Objective-C的运行时和关于内部函数如何运行的错综复杂的细节。这会让我们能够更深入的理解应用程序。

<br/>
### Objective-C runtime 

Objective-C是一个面向运行时的语言。所以问题就是，什么是一个运行时语言？一个运行时语言就是在应用程序运行的时候来决定函数内部实现什么以及做出其它决定的语言。Objective-C是一个运行时语言吗？不是。它是一个面向运行时的语言，这意味着只要有可能，它就把做决定的时间从编译时和链接时延迟到这段代码真正执行的时候。正如前面指出的，Cocoa提供了iOS应用程序所需要的运行时环境。下面的截图来自苹果官方的文档，说的非常清楚。你可以到[这][1]阅读这个文档。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat1.png)


我们来看看运行时库是否被工程引入了。理想情况下，它应该存在于每个iOS应用中。登录进设备，然后进入到应用程序目录。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat2.png)


在命令行中输入“ls *”

 ![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat3.png)


现在来看看BADLAND iOS应用程序。BADLAND是一个流行的iOS游戏。进入到BadLand的目录内，一旦进入到BADLAND.app目录内，对BadLand的二进制文件使用otool。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat4.png)


可以看到引入了很多的framework以及库文件。objc-runtime也在其中，如图：

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat5.png)


这个运行时库使得在Objective-C中进行运行时操作变得可能。默认所有iOS应用都包含这个库。下面是对Google Maps应用使用otool得到的结果，你可以看到，它也包含了Objective-C 运行时库。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat6.png)


<br/>
### 使用GDB进行运行时分析

在本节，我们将使用GDB来观察程序的流程。第一件事情就是安装一个合适版本的gdb。从Cydia能获得的gdb版本并不能工作正常。因此，确保你从其他地方获得gdb，然后可以通过sftp安装到设备上。如图：

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat7.png)



确保你有能够运行的权限。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat8.png)


为了能够挂钩（hook）进一个运行的进程，第一件事就是确保这个进程正在运行。在这里，我们将在Google Maps应用上测试。首先我们启动应用，然后获得它的进程id。确保它在前台运行。如下图所示，Google Maps的进程id是661。请注意这个id在你的设备上可能不同。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat9.png)


现在，我们用GDB 挂钩这个进程。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat10.png)

你可以看到，我们成功挂钩进入这个进程。目前我们可以忽略其中的警告。

Objective-C是基于消息的，一旦一个消息被发出，objc_msgSend就会被调用。为了能够分析应用的运行流程，我将给一个最基本的调用下一个断点，例如objc_msgSend, 并且打印出$r0和$r1的值。从$r0我们可以知道这个方法对应的类是什么，从$r1我们可以知道selector。请注意，即使是这样，也可能会打印出太多的细节信息，因为objc_msgSend在消息发出的每个地方都会被调用。在接下来的文章里，我们将看看如何能够更有效使用它。因此，基本上只要断点断下来，我就将打印出$r0和$r1的值然后继续运行程序。下面是如何做的。

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat12.png)


输入 c 继续运行程序

![](http://resources.infosecinstitute.com/wp-content/uploads/060313_1218_IOSApplicat13.png)


如你所看到的那样，这个在分析应用的执行流程的时候很有用。这里打印太多信息了，因此在实际分析应用的时候，不会太有用处，不过在这是为了让大家知道我们能够看到应用是如何实现的。


<br/>
### Method Swizzling

我们已经学到许多iOS应用都使用运行时环境、并且在运行时再做决定。正如其名，method swizzling是我们用来修改iOS程序的另一个武器，我们可以用它来调用我们自己的方法。在下一篇文章中，我们将详细介绍Method Swizzling。

<br/>
参考:

 [The Dark art of IOS Application hacking][2]

[The Objective-C runtime][3]


<br/>
本文原文是 [IOS Application security Part 3 – Understanding the Objective-C Runtime][4]


[1]:https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ObjCRuntimeGuide/Introduction/Introduction.html
[2]:http://www.slideshare.net/daniel_bilar/blackhat2012-zdziarskidarkarti-osapplicationhacking/
[3]:http://www.slideshare.net/mobiledatasolutions/objectivec-runtime
[4]:http://resources.infosecinstitute.com/ios-application-security-part-3-understanding-the-objective-c-runtime/
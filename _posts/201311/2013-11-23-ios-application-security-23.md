---
layout: post  
title: iOS应用程序安全(23)-对抗运行时分析和操作  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-23-defending-runtime-analysis-manipulation/  
版权声明：自由转载-非商用-保持署名


在前面的文章中，我们看到了如何使用调试器和工具如Cycript对iOS应用进行运行时分析和操作。我们也看到了在运行时通过使用GDB来修改寄存器的值是如何修改方法的具体实现的，看到了怎样使用工具如Cycript来进行[swizzling][1]方法实现。有Cycript和GDB这样的工具在手，加上你的应用程序可执行文件的拷贝，一切就都在攻击者的掌控之中。不过，有一些技巧能够使得攻击者更难攻击。本文我们将查看开发者能够用来对抗运行时分析和操作的防御技巧。

在Xcode中，开发者（译者注：原文写的是攻击者，按上下文理解应该是开发者）能够使用一些检测方法来查看一个应用是否正被调试。在Xcode中，你可以用下面的代码来检查调试器是否存在。

    #ifndef DEBUG
    SEC_IS_BEING_DEBUGGED_RETURN_NIL();
    #endif


正如名字指出的，如果应用正被调试，那么这个宏会返回nil。你可以把这个检查放到一些重要的地方，比如访问或者返回重要数据的地方。如果在那时（访问或者返回重要数据的时候）应用正被调试，这个宏就会返回nil。因此你的应用就不会工作正常，因此攻击者就会遇到问题。或者你可以在一个Timer中调用，一旦你发现应用正被调试，你可以删除存储在应用中的所有重要信息、重要文件等等。但是，请注意攻击者使用Cycript能够劫持你的方法实现，因此比较明智的是只使用一个宏而不是在一个方法内使用这个宏。请注意这个宏只在release下能工作正常。要在你的设备上测试，你需要在build中选择release。选择scheme，然后点击Edit Scheme。

![](http://resources.infosecinstitute.com/wp-content/uploads/111313_1602_IOSApplicat1.png)

在Info下面，把Build Configuration设置为Release。

![](http://resources.infosecinstitute.com/wp-content/uploads/111313_1602_IOSApplicat2.png)


现在你可以使用Xcode在你的设备上运行这个应用，你会看到成功检测到调试器。这是因为Xcode会给正在运行的应用附加一个调试器。


再次说一下，这个方法并不能保证应用不会被调试。一个技艺熟练的攻击者能够对应用程序的二进制文件对这个宏相关的汇编指令打补丁。作为开发者，你应该在多个地方检查，使得攻击者更难调试。

另一个阻止调试器附加到应用程序的方法是使用ptrace函数。使用这个函数，传递一个特定的参数，能够阻止其它任何调试器附加到应用程序上。像GDB和LLDB在附加到进程的时候就会使用ptrace函数。使用ptrace，加上参数`PT_DENY_ATTACH`就会告诉这个函数，应用不允许追踪。下面是[苹果官方文档][2]关于
`PT_DENY_ATTACH` 的截图。

![](http://resources.infosecinstitute.com/wp-content/uploads/111313_1602_IOSApplicat3.png)

让我们试试。在Xcode中创建一个新工程。点击File->New->Project->Single View Application。然后在模拟器中运行。你会得到如下的视图。

![](http://resources.infosecinstitute.com/wp-content/uploads/111313_1602_IOSApplicat4.png)

现在，使用如下的代码来修改main.m文件。导入ptrace.文件，然后在main函数中增加如下代码。


    #ifndef DEBUG
    ptrace(PT_DENY_ATTACH, 0, 0, 0);
    #endif
    
你的main.m看起来应该像这样。


![](http://resources.infosecinstitute.com/wp-content/uploads/111313_1602_IOSApplicat5.png)

现在运行程序，你会发现应用加载不起来。这是因为当Xcode加载应用的时候，它会把一个调试器(LLDB或者GDB)附加到应用上。因为我们在main.m中加了反调试代码，因此Xcode不能附加调试器，因此它就退出应用。

双击模拟器上的home按钮，然后kill这个测试应用。再次运行，这次能够启动起来。这是因为我们没有通过Xcode加载，因此没有调试器附加到上面。

当然，这个方法也不能保证你的应用就绝对安全。技术熟练的攻击者能够在解密应用之后对这个防检测代码打补丁。作为开发者，你应该在多个地方使用这个方法，使得攻击者的工作变得更难。


需要注意的事，上述两种方法都将试图阻止调试器加载到应用上，但是它对Cycript没有用，因为Cycrit并不追踪应用。作为开发者，你能够做下面的一些检查使得攻击者的工作变得尽可能的难。例如，你可以在应用中增加一些假的方法，攻击者可能会对这些假方法感兴趣。例如，用`userDidLogin:(BOOL)didLogin; `作为名字，肯定会吸引攻击者的注意。当然，攻击者会试图用Cycript执行。在这个方法实现中，你可以清除应用中的所有数据，甚至报告服务器，这个应用已经被侵入。对于特别需要安全的银行应用，你也可以检查设备是否已经越狱。如果是，可以拒绝所有访问服务器并且删除所有存在应用本地的重要信息。在下一篇文章中，我们将查看如何检查设备是否越狱。另一件需要做的事情就是检查应用是否被破解，然后执行必要的步骤来防止攻击者获取信息。在[github][3]上有一个很简单的类可以帮你完成这个工作。当然，攻击者总是能够劫持方法实现的，因此，把它改一个不那么起疑的名字。

对于那些检查应用是否安全的方法，你也可以给它们改个名字以便它们看起来不那么重要。例如，一个检查应用是否正被调试的方法不应该被命名为`-(BOOL)isAppBeingDebugged`。这肯定将会引起攻击者的注意并替换方法实现。相反，命名为`-(BOOL)didChangeColor` 或者 `-(BOOL)didSetFont`。就不错。这个情况下，这个函数在攻击者看来就不那么重要了。


如果你已经检测到可疑行为，并且认为最好退出应用，你可以使用下面的命令。

    exit(-1);

这将退出应用。

最好，一个需要注意的非常重要的一点就是，没有任何应用在一个技艺熟练的攻击者面前是安全的。有你的应用的二进制文件的拷贝，加上Cycript、GDB等工具在手，一切就都在攻击者的掌控之中。我们能做的就是使用尽可能多的检查，使得攻击者的任务变得困难以至于他放弃我们的应用去找那些更容易攻击的应用。




译者注1：第一种方法，我在Xcode5上没有试成功，如果有人知道原因，麻烦说一下。   
译者注2；SEC_IS_BEING_DEBUGGED_RETURN_NIL是一个自定义的宏，请参见[iOS App Security and Analysis: Part 2/2][4] 和 [这][5] （感谢小伙伴 方彬@支付宝 给我提示）


   <br/>
本文原文 [IOS Application Security Part 23 – Defending against runtime analysis and manipulation][3]



[1]:http://wufawei.com/2013/11/ios-application-security-8/
[2]:https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man2/ptrace.2.html
[3]:http://resources.infosecinstitute.com/ios-application-security-part-23-defending-runtime-analysis-manipulation/
[4]:http://www.raywenderlich.com/46223/ios-app-security-analysis-part-2
[5]:https://github.com/x128/MemeCollector/blob/master/Meme%20Collector/NSObject%2BdebugCheck.h


---
layout: post  
title: iOS应用程序安全(2)-获得iOS应用程序的类信息   
categories:  
- iOS  
tags:    
- iOS Security
---   


作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-2-getting-class-information-of-ios-apps/  
版权声明：自由转载-非商用-保持署名

###介绍

你是否下载过非常cool的app（不管它是预装应用还是从App store下载的应用），然后想知道它是如何编写的，它使用了哪些第3方库呢？是否想知道这个app内部代码是如何组织的？你是否想导出它内部的图片（images）、plist文件？如果答案是想的话，那你来对地方了，本文就将解答这些信息。


本文我们将分析应用（不管是预装应用还是从App store下载的应用）内部的类（class）是如何使用的，它的view controller使用的view的名称，它内部使用的库，甚至更复杂的，例如变量和方法的名称。我们将解密（decrypt）从App store下载的应用，导出它所有的image、plist文件。

点击[此][1]查看上一篇文章。

<br>
###导出设备上预装应用的类信息

现在我们能够分析应用的类信息了，先试试导出Apple的Maps应用。第一步是定位Apple Maps应用的可执行文件的位置。
所有iOS预装的应用都位于/Applications目录下。因此，先转到那个目录。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat1.png)

在这你可以看到所有预装的应用。进入Maps应用的目录然后执行list命令:

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat2.png)


正如你所见，我们可以看到Maps应用使用的所有的image、plist文件。随后我们将讨论如何获取一个特定的应用的所有image文件或者其他文件。如下图所示，Maps.app目录里面有个文件叫做Maps。请注意可执行程序的名称和app的名称相同。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat3.png)


为了导出类信息，在命令行下输入 class-dump-z Maps

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat4.png)


你会在命令行看到太多输出，最好是输出到一个文件，这里我们把它输出到名叫class-dump-Maps的文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/042613_1143_IOSApplicat5.png)


你可以使用sftp登录设备然后下载这个文件。你可以使用命令get，如下图所示。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat6.png)


现在文件已经下载到电脑上，可以用TextMate打开。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat7.png)


我们从接口文件看到代码是如何设计的。例如，在这你可以看到一个InfoCardController。或许你已经猜到，当我们点击下面图片中的位置信息的向右箭头的时候，就是这个VC用来展示这个地址的更多信息的。

![](http://resources.infosecinstitute.com/wp-content/uploads/042613_1143_IOSApplicat8.png)



我们看看应用中的这个view。这一页实际上是InfoCardViewController展示的。

![](http://resources.infosecinstitute.com/wp-content/uploads/042613_1143_IOSApplicat9.png)

如果你看到这个图以及上面导出的类信息，你可以很容易的知道当你点击上面的按钮的时候，哪些对应的方法会被调用。例如，如果你点击了“Direction to here”, 那

`-(void)_directionsTo:(id)to person:(void*)person property:(int)property identifier:(int)identifier;`

这个方法会被调用。

类似的，如果点击了“Add to Bookmarks” , 那

`-(void)_addToBookmarks:(id)bookmarks person:(void*)person property:(int)property identifier:(int)identifier;`

这个方法会被调用。 

你也可以从这个应用里找出很多其他信息，例如有个UserLocationSearchResults继承自SearchResult。

![](http://resources.infosecinstitute.com/wp-content/uploads/042613_1143_IOSApplicat10.png)


你能获得多少信息，完全在于你的好奇心有多少！

<br>
# 导出从App store下载的app的类信息

如果你想分析从App store下载的app，有2个重要的事情你要先知道：  
* 这些应用存放在另一个地方，/var/mobile/Applications/  
* 和预装的app不同，这些应用是加密了的，因此你首先需要解密一下。

为了解密这些应用，我们将使用一个叫做Clutch的命令行工具。请注意提供Clutch的Hackuous已经于数月前被关闭。但是在网上能够找到Clutch的可执行文件。

如果你已经下载到了Clutch，现在要做的事情就是把它上传到你的越狱设备上。可以使用sftp，使用的方法如图：

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat11.png)



现在用ssh登录设备，然后输入clutch。然后你可以得到所有可以被破解的应用列表。

![](http://resources.infosecinstitute.com/wp-content/uploads/042613_1143_IOSApplicat12.png)


为了破解某个应用，输入 “clutch 应用名称” 即可。例如，如果我们想破解Facebook这个应用，输入clutch Facebook

![](http://resources.infosecinstitute.com/wp-content/uploads/042613_1143_IOSApplicat13.png)


一旦破解完成，clutch会告诉你它保存ipa文件的位置。现在ipa只是整个app文件的压缩版本。使用命令行unzip解压，用-d指定解压的目录，如下图所示。你也可以用sftp把ipa拷贝到你电脑上，然后解压。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat14.png)


现在我们已经获得了解密之后的文件，我们就可以使用class-dump-z来导出类信息，并把它保存到class-info-Facebook目录。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat15.png)

一旦导出完成，你可以退出ssh，然后用sftp登录并下载class-info-Facebook文件。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat16.png)


现在你可以用文本编辑器查看这个文件。例如，这里有个名叫FBFacebookRequestSender的协议用来发送异步请求，并且有一个方法用来验证session是否有效。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat17.png)



<br>
###从应用获得图片和其他文件

正如前文讨论的，你可以使用sftp从应用中下载你需要的所有文件。不过，有更方便的方法来下载文件，例如使用[iExplorer][2]。从其官方网站上下载，安装之后打开它，确保你的设备通过USB连接到系统。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat18.png)



要查看文件系统，点击files

![](http://resources.infosecinstitute.com/wp-content/uploads/042613_1143_IOSApplicat19.png)


要查看某个特定的应用，点击Apps

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat20.png)


如你所见，浏览文件和上传、下载文件都非常容易。在这里，要下载Facebook应用中的image和其他文件。在左边栏，点击Facebook，然后在右边右键，就可以导出所有文件。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042613_1143_IOSApplicat21.png)


<br/>
###总结

在本文的前两部分，我们学习了如何在越狱设备上搭建一个移动审计环境。然后我们学到了如何导出任意应用的类信息，利用类信息来学习理解代码的设计和内部是如何工作的。我们也学到了如何解密和审计从App store下载的应用。我们也学到了如何使用sftp和iExplorer来下载、上传文件。

通过使用class-dump-z导出类信息, 能够获得所有的调用方法。有没有可能通过某种方式来进行运行时的修改呢？

例如，如果一个方法如(BOOL)isFacebookSessionValid 在某种情况下返回false，是否有可能操作应用，使得它返回YES，因此让应用去做一些未知的事情？进一步的，是否有可能创建我们自己的方法并且在isFacebookSessionValid 执行时执行我们自己的方法呢？
是否有可能在运行时、或者某个特定指令之后修改一个应用的实例变量呢？

**这个答案是：可以（YES）**。我们将在接下来的文章中学习。


<br/>
本文原文是 [IOS Application security Part 2 – Getting class information of IOS apps][3]

[1]: http://wufawei.com/2013/11/ios-application-security-1/
[2]: http://www.macroplant.com/iexplorer/download-ie3-mac.php
[3]: http://resources.infosecinstitute.com/ios-application-security-part-2-getting-class-information-of-ios-apps/
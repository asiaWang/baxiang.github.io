---
layout: post  
title: iOS应用程序安全(7)-不用证书安装应用到设备的方法  
categories:  
- iOS  
tags:    
- iOS Security
---   


作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-7-installing-and-running-custom-applications-on-device-without-a-registered-developer-account/  
版权声明：自由转载-非商用-保持署名


##引言

通常情况下，如果你要在设备上测试应用，你需要每年花99美元注册成为开发者。对于想要学习iOS应用安全的人来说，能够在设备上运行并测试应用非常重要。对于不想在app store发布任何程序的人来说，每年99美元并不值得。本文我们将看看在没有注册成为开发者账号的情况下，如何在越狱设备上构建和安装应用。在接下来的文章中，我们将看看如何在设备上运行我们自己的应用，使用Cycript来做method swizzling和其它一些技巧。

本文将使用Xcode 4.5.2，设备是iOS5.1的系统。同样的技巧可能在其他版本的的iOS或者Xcode有效，但是也不一定。如果你在运行应用的时候遇到同样的问题，请留言，我会尽快回复。

第一步是创建一个自签名的证书。我们将使用这个证书来签名我们的应用。

打开Keychain Access应用。点击keychain Access -> Certificate Assistant -> 创建一个证书

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat1.png)

命名这个证书，在这里我们命名为 Prateekg, 选择证书类型为 Code Signing, 不要选中 Let me override defaults。
点击创建和继续按钮。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat2.png)

如你所见，我们证书已经创建好了。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat3.png)

现在把文件从/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Info.plist的Info.plist文件复制到桌面。这么做的原因是我们要编辑这个文件，但是在原位置是不能这么做的。因此，我们需要先复制到桌面，编辑后，再拷贝回原来位置。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat4.png)


打开桌面上的文件

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat5.png)

在这个文件中，把所有的XCiPhoneOSCodeSignContext 替换为 XCCodeSignContext，保存。现在你可以看到文件内容如下：

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat6.png)

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat7.png)

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat8.png)

现在把这个文件放回原来的位置。你可能需要相应的权限才能这么做。我用的命令是
sudo cp /Users/prateekgianchandani/Desktop/Info.plist /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Info.plist

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat9.png)

现在是时候创建一个例子工程了。打开Xcode, 点击创建一个新的Xcode项目，选择单视图应用。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat10.png)

你可以随意命名，这里我们命名为SelfSignedApp,点击下一个，然后点击继续。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat11.png)


因为我们将要在运行iOS 5.1的设备上运行，我们需要确保部署目标是对的。在左上的项目导航区选择工程名字，然后在Info里面，
设置部署目标为5.1，如下图所示。你可以根据你的设备运行的操作系统版本来选择部署目标。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat12.png)


因为我们在iOS 5.1上运行，并且我们用了Storyboard，但是iOS5.1并不支持Autolayout，请确保Use Autolayout选项没有被选中。
否则程序可能会因此crash。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat13.png)

现在开始拖放label到storyboard，在上面写些文字。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat14.png)


现在我们要让Xcode用我们自己生成的证书去签名。在左上的项目导航区选择工程名字，然后在Build Settings中选择我们生成的证书。如果因为某些原因，在下拉框中并没有显示我们的证书，请重启Xcode。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat15.png)


现在选择我们的应用要运行的设备。如果设备并没有在列表中显示，请到Organizer－> Devices -> 你的设备，选择  Use for development.

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat16.png)


选择设备之后，点击运行，你会得到如下图的一个警告。点击总是允许。你可能会被提示设备或者Xcode错误，那请断开设备连接，重新打开Xcode，再次运行，这样应用就能部署到设备上。

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat17.png)


也有其他方法让应用能够在设备上运行。构建应用，然后Xcode会在一个特定的目录创建一个.app文件。
默认地址是：/Users/$[YOUR_USER_NAME]/Library/Developer/Xcode/DerivedData/$[YOUR_APP_NAME_APP_ID]/Build/Products/Debug-iphoneos/. 在我的系统上，地址是：/Users/prateekgianchandani/Library/Developer/Xcode/DerivedData/SelfSignedApp-bfzixtyoynrxxlgigskifizrfqqw/Build/Products/Debug-iphoneos/


用下面的命令复制这个.app文件到桌面上
mv /Users/$[YOUR_USER_NAME]/Library/Developer/Xcode/DerivedData/$[YOUR_APP_NAME_APP_ID]/Build/Products/Debug-iphoneos/SelfSignedApp.app /Users/$[YOUR_USER_NAME]/Desktop/

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat18.png)


![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat19.png)

现在创建一个目录叫做Payload,然后把SelfSigned.app放到里面。然后压缩Payload目录（压缩文件会被命名为
Payload.zip）然后重命名Payload.zip为SelfSigned.ipa. 从这个系列的第2篇文章可以看到，这就是iOS应用保存的包。


一旦我们有这个ipa文件，我们有2种方法来安装它。第一种就是直接把这个ipa拖到iTunes的apps部分，然后用iTunes把应用安装到设备上。

另一个方法就是用sftp把ipa上传到设备上，然后用一个叫做installipa的工具来安装它。可以通过Cydia来下载installipa.

用sftp上传ipa文件到设备

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat20.png)

然后用ssh登录，在命令行用installipa安装

![](http://resources.infosecinstitute.com/wp-content/uploads/070813_1648_IOSApplicat21.png)


这样就可以把应用安装到你的设备上了。你可能需要重启设备来让应用正常工作。


##总结

本文我们介绍了如何在没有开发者证书的情况下把应用安装到越狱设备上。接下来，我们将用这些技巧来把我们的应用安装到设备上，然后在设备上做各种测试。



<br/>
本文原文是 [IOS Application Security Part 7 – Installing and Running Custom Applications on Device without a registered developer account][1]

[1]:http://resources.infosecinstitute.com/ios-application-security-part-7-installing-and-running-custom-applications-on-device-without-a-registered-developer-account/
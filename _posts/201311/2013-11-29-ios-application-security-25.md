---
layout: post 
title: iOS应用程序安全(25)-iOS开发安全编程实践 
categories: 
- iOS 
tags:   
- iOS Security
---  


作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-25-secure-coding-practices-ios-development/ 
版权声明：自由转载-非商用-保持署名
 
本文我们将探讨iOS开发者应该遵循的最佳安全实践，使得应用不那么容易被攻击者利用。
<br>
##本地数据存储

对开发者来说非常重要的一点就是要知道哪些数据需要在应用本地存储。坦率的说，存储在应用本地的数据都是不安全的。在本系列的[第20篇文章][1]中，我们详细查看了本地数据存储。

* 重要数据如密码，会话ID等等绝不要存储在设备上。如果别无它法，那么应该存在keychain中。这是因为只要这个设备不越狱，攻击者不能从
 keychain中找出这些数据。因为超过70%的人都已经把他们的设备升级到iOS 7并且目前iOS 7还不能越狱，你能确定攻击者目前将不能够从keychain中获取数据。有人可能会说
把数据保存到keychain不像把数据保存到NSUserDefaults那么简单。不过我们能够使用第3方封装的代码使得这个过程变得极其简单。例如，
[这一篇文章][2]演示了如何使用PDKeychainBindings这个wrapper，展示了把数据保存到keychain中是多么的简单。下面就是用这个wrapper来把数据保存到keychain的代码示例。



         PDKeychainBindings *bindings = [PDKeychainBindings sharedKeychainBindings];
         [[[Model sharedModel] currentUser] setAuthToken:[bindings objectForKey:@"authToken"]];
    


不过，请注意在越狱设备上，keychain中的信息并不安全。一个可取的方法就是在把字符串保存到keychain之前，先用你自己的加密方法加密一下。这样就有更高的安全性，
因为即使攻击者从keychain中拿到这个加密字符串，他也不得不解密这个加密后的字符串。


* 绝对不要把机密信息如密码，认证令牌等信息保存到NSUserDefaults。这是因为所有保存到NSUserDefaults的信息都是以未经加密的格式保存在一个plist文件的，位于你的应用程序
bundle的Library -> Preferences -> $AppBundleId.plist。任何人都能够使用工具如iExplorer来窥视你的应用程序的bundle，然后得到这个plist文件，即使你的设备未越狱。


* 绝对不要把机密信息如密码等信息保存到Plist文件，因为即使在未越狱设备上要获取这些plist文件也非常容易。所有保存到plist文件的内容都是以未加密的格式保存的。

* Cora Data文件同样是以未加密的数据库文件保存在应用程序bundle的。Core Data framework内部使用Sql查询来保存数据，因此所有文件都是.db文件。非常容易就能把这些文件复制到电脑上，
然后用工具如sqlite3就能查看这些数据库文件中的所有内容。



<br>
##传输层安全

  * 发布应用的时候不要允许使用自签名证书。大多数开发者在debug模式的时候会允许自签名证书，但是发布应用的时候，这一点要避免。
  * 不要使用设备唯一的参数（MAC地址， IP，UDID）来决定会话ID，认证令牌等等。
 * 重要的决定，比如认证和授权应该放在后台。请记住攻击者能够在运行时操纵你的应用。
 * 应当在客户端和服务端都做适当的输入验证。攻击者能够使用Burpsuite更改请求。非常重要的一点就是验证发到后台的参数，避免任何形式的注入攻击。

<br>
##使用加密

 * 在保存重要文件之前先加密。要加密这些文件，你不必是一位密码学专家。有许多的第3方库能够为你完成这个工作。
我曾经写过[一篇文章][3]，介绍使用[RNCryptor][4]（可以从github下载）来加密图片并保持到应用沙盒。




>     UIImage *imageToEncrypt = [UIImage imageNamed:@"SomeImage"];
>     NSString *imagePath = [NSHomeDirectory() stringByAppendingPathComponent:@"Documents/encryptedImage.png"];
>     NSData *data = UIImagePNGRepresentation(fetchedImage);
>     NSError *error;
>     NSData *encryptedData = [RNEncryptor encryptData:data withSettings:kRNCryptorAES256Settings password:@"ABC123" error:&error];
>     [encryptedData writeToFile:imagePath atomically:YES];



   
  要加密SQLite文件，你应当考虑[SQLCipher][5]


<br>
##增加检查以避免运行时分析

* 请记住只要有你的应用的二进制文件的拷贝，那一切都在攻击者控制之中。因此要使得攻击者分析的过程变得尽可能的难。其中一个方法就是阻止调试器附加到应用上。
正如我们在本系列文章的[第23篇][6]中介绍的那样。你的main.m文件看起来应该像这样...

![](http://resources.infosecinstitute.com/wp-content/uploads/112713_0635_IOSApplicat1.png)


这将会阻止攻击者附加到你的应用上。我们已经学过如何使用[Snoop-it][7]来追踪方法调用。下面是从同一篇文章的截图。


![](http://resources.infosecinstitute.com/wp-content/uploads/112713_0635_IOSApplicat2.png)

使用了上述添加到main.m文件的那些代码，现在我们就不能这么做了。这是因为Snoop-it是在应用启动的时候，把调试器附加到应用上来追踪方法调用的，现在有了上述检查，它就不能再这么
做了并且应用会crash。请注意这不会阻止工具如Cycript因为它并不追踪应用的方法调用。

<br>
## 其他一些事情

* 用来输入密码的TextFields应用使用Secure选项。这是因为如果不使用Secure标签的话，iOS通常会缓存你输入到textfields的东西。请同时也禁用这些TextFields的AutoCorrection。如下图
所示，你可以看到textfield的AutoCorrection被设置为NO，并且启用了Secure标签。

![](http://resources.infosecinstitute.com/wp-content/uploads/112713_0635_IOSApplicat3.png)

* 应用进入后台的时候应该清除剪贴板。你可以在AppDelegate的- (void)applicationDidEnterBackground:(UIApplication *)application 添加下面的代码。如果你使用自定义的剪贴板，
 用自定义的剪贴板替换[UIPasteboard generalPasteboard] 。


>     - (void)applicationDidEnterBackground:(UIApplication *)application
>     {
>     // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
>     // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
>     [UIPasteboard generalPasteboard].items = nil;
>     }

* 使用URL schemes做些重要事情的时候要增加提示或者验证。我们知道任意应用都能注册一个URL Scheme。例如Skype应用能够注册URL Scheme skype://并且任意应用都能用参数调用这个url。
这就使得应用之间能够通信。在之前，Skype有一个漏洞，使得任意用户都能使用如下的url， skype://123123123?call，呼叫任何人。因为Skype呼叫之前并没有提示用户，这些就被直接发送出去了。
在真正发出呼叫之前，提示一下用户会更好一些。URL shceme的输入也同样需要被验证。你可以把验证放在AppDelegate的- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url 中。


>     – (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url {
>     //Validate input from the url
>     return YES;
>     }





* 有些应用使用UIWebView来展示来自URL的内容。在一些流行应用中已经发现了UIWebViews的[漏洞][7]。UIWebViews也支持javascript，而且目前没有公开的API来禁用UIWebView中的javascript。因此，如果用户控制的任何输入被用作UIWebView的内容，它就可能被操纵在运行时在UIWebView中执行javascript代码。即使这个输入不受用户控制，攻击者也能够在运行时操纵添加到UIWebView的内容，因此执行他想执行的任意javascript代码。因为苹果所加的限制，开发者对此能做的也不多，开发者应该要通过如下方法来确保加载进UIWebView的内容不是恶意的，a）通过HTTPs加载数据。b)确保UIWebView的内容不依赖于用户的输入。c）通过NSData类提供的dataWithContentsOfURL函数来验证URL的内容。



[1]:http://wufawei.com/2013/11/ios-application-security-20/
[2]:http://highaltitudehacks.com/2013/09/17/ios-dev-storing-info-in-keychain-with-nsuserdefaults-like-syntax/
[3]:http://highaltitudehacks.com/2013/09/26/ios-dev-encrypted-images-and-saving-them-in-app-sandbox/
[4]:https://github.com/rnapier/RNCryptor
[5]:http://sqlcipher.net/
[6]:http://wufawei.com/2013/11/ios-application-security-23/
[7]:https://superevr.com/blog/2011/xss-in-skype-for-ios/














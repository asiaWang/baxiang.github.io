---
layout: post  
title: iOS应用程序安全(12)-导出Keychain数据  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-12-dumping-keychain-data/  
版权声明：自由转载-非商用-保持署名



在前面的文章中，我们查看了如何用不同的方法来分析应用的网络流量和API调用。本文我们将会从iOS设备中导出keychain的内容。

<br>
## Keychain 基础

根据苹果的介绍，iOS设备中的Keychain是一个安全的存储容器，可以用来为不同应用保存敏感信息比如用户名，密码，网络密码，认证令牌。苹果自己用keychain来保存Wi-Fi网络密码，VPN凭证等等。它是一个sqlite数据库，位于/private/var/Keychains/keychain-2.db，其保存的所有数据都是加密过的。

开发者通常会希望能够利用操作系统提供的功能来保存凭证（credentials）而不是把它们（凭证）保存到NSUserDefaults,plist文件等地方。保存这些数据的原因是开发者不想用户每次都要登录，因此会把认证信息保存到设备上的某个地方并且在用户再次打开应用的时候用这些数据自动登录。Keychain的信息是存在于每个应用（app）的沙盒之外的。

通过keychain access groups可以在应用之间共享keychain中的数据。要求在保存数据到keychain的时候指定group。把数据保存到keychain的最好方法就是用苹果提供的KeychainItemWrapper。可以到[这][1]下载例子工程。第一步就是创建这个类的实例。

KeychainItemWrapper *wrapper = [[KeychainItemWrapper alloc] initWithIdentifier:@”Password” accessGroup:nil];

标识符（Identifier）在后面我们要从keychain中取数据的时候会用到。如果你想要在应用之间共享信息，那么你需要指定访问组（access group）。有同样的访问组
的应用能够访问同样的keychain信息。

KeychainItemWrapper *wrapper = [[KeychainItemWrapper alloc] initWithIdentifier:@”Account Number” accessGroup:@”YOUR_APP_ID_HERE.com.yourcompany.GenericKeychainSuite”];


要把信息保存到keychain中，使用 setObject:forKey: 方法。在这里， (id)kSecAttrAccount 是一个预先定义好的键（key），我们可以用它来保存账号名称。
kSecClass指定了我们要保存的某类信息，在这里是一个通用的密码。kSecValueData可以被用来保存任意的数据，在这里是一个密码。


[keychainItemWrapper setObject:kSecClassGenericPassword forKey:(id)kSecClass];

[wrapper setObject:@"username" forKey:(id)kSecAttrAccount];

[keychainItemWrapper setObject:@"password"forKey:(id)kSecValueData];

[wrapper setObject:(id)kSecAttrAccessibleAlwaysThisDeviceOnly forKey:(id)kSecAttrAccessible];


kSecAttrAccessiblein变量用来指定这个应用合适需要访问这个数据。我们需要对这个选项特别注意，并且使用最严格的选项。这个键（key）可以设置6种值。

你可以从如下对苹果的[文档][2]的截图看到。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/080513_1630_IOSApplicat1.png)



当然，我们应该绝对不要使用kSecAttrAccessibleAlways。一个安全点的选项是kSecAttrAccessibleWhenUnlocked。这也有些选项是以 ThisDeviceOnly 结尾的。如果选中了这个选项，那么数据就会被以硬件相关的密钥（key）加密，因此不能被传输到或者被其他设备看到。即使它们提供了进一步的安全性，使用它们可能不是一个好主意，除非你有
一个更好的理由不允许数据在备份之间迁移。

要从keychain中获取数据，可以用
NSString *accountName = [wrapper objectForKey:(id)kSecAttrAccount];


<br>
## 使用Snoop-it分析Keychain读写


一个用来分析写到Keychain中的非常棒的工具就是Snoop-it。如果你还不知道Snoop-it，请查阅本系列的[第9篇关于Snoop-it文章][3]。现在在你的越狱设备上运行工程[GenericKeychain][4]，然后用Snoop-it来分析它。本系列[第7篇][5]介绍如何用自签名的证书来让应用运行。这个应用使用同样的例子工程来演示如何使用Objective-C的一个wrapper类KeychainWrapper来读写keychain。请确保这个应用正被Snoop-it分析。现在打开应用，我们将看到如下的界面。


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat2.png)


现在随意输入用户名和密码然后保存。这里我们输入用户名为“Test User”，密码为“password”。你可以看到Snoop-it可以检测到keychain的任何变话，然后告诉我们
保存在keychain的信息。同时也会告诉我们保存的信息的保护级别（protectionn class)。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/080513_1630_IOSApplicat3.png)


这里的信息给出了我们的用户名和密码。


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat4.png)

现在输入账号。Snoop-it同样也能检测到。

![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat5.png)

你可以看到，Snoop-it也能购检测到对keychain数据库的操作。


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat6.png)


<br>
##  使用Keychain Dumper导出Keychain中的数据

从Keychain中导出数据的最流行工具是ptoomey3的Keychain dumper。其github地址位于[此][6]。现在到这个地址把它下载下来。然后解压zip文件。在解压的文件夹内，我们感兴趣的文件是keychain_dumper这个二进制文件。一个应用能够访问的keychain数据是通过其entitlements文件指定的。keychain_dumper使用一个自签名文件，带有一个*通配符的entitlments，因此它能够访问keychain中的所有条目。 当然，也有其他方法来使得所有keychain信息都被授权，比如用一个包含所有访问组(access group)的entitlements文件，或者使用一个特定的访问组（access group）使得能够访问所有的keychain数据。
例如，工具[Keychain-viewer][7]就使用如下的entitlements.

com.apple.keystore.access-keychain-keys

com.apple.keystore.device


现在把这个二进制文件上传到你的设备的/tmp文件夹，确保它可执行。


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat7.png)


现在请确保保存在/private/var/Keychains/keychain-2.db的keychain文件可以被读取。


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat8.png)



现在，运行这个可执行文件


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat9.png)



![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat10.png)



如你所见，它可以导出所有的keychain信息。你可以看到许多保存在这里的用户名和密码。例如，你可以看到Mail应用把你账号的用户名和密码保存在keychain中。
类似的，你也可以找到你之前连接过的无线网络的密码和其他更多的信息。上述命令默认只会导出通用和网络密码。你可以通过－h命令查看它的用法。



![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat11.png)





你可以通过 “-a” 命令导出所有数据

![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat12.png)


使得keychain中的数据更安全的一个做法就是使用一个更强的口令。这是因为对某些特定的保护属性（protection attributes）来说，口令会被用作加密keychain中的数据。
iOS默认允许4位数字口令（从0-9999），因此很容易被人在几分钟之内暴力破解。本系列的后续文章中我们会看看暴力破解口令。设置字母加数字的密码会让破解花更多的时间。
一个合适的保护属性（protection attributes）和口令的组合会让keychain的数据更难被获取。

<br>
## 总结

在本文中，我们看到了从iOS设备的Keychain中导出数据是多么的容易。虽然在keychain中保存凭证（credentials）和敏感信息比NSUserDefaults和plist文件更安全，但是，
想要破解它也不难。



References

Keychain-Dumper
https://github.com/ptoomey3/Keychain-Dumper

<br/>
本文原文是 [IOS Application Security Part 12 – Dumping Keychain Data][9]

注：之前我写过一篇文章，[Keychain is not safe][8],大家可以对照着看看，有的应用还是在keychain中保存密码。虽然本文说keychain也容易被破解，不过比NSUserDefaults和plist安全得多，只要我们注意不要在keychain中保存明文密码就会在很大程度上提升安全性。


[1]:http://developer.apple.com/library/ios/
[2]:http://developer.apple.com/library/mac/
[3]:http://wufawei.com/2013/11/ios-application-security-9/
[4]:http://developer.apple.com/library/ios/
[5]:http://wufawei.com/2013/11/ios-application-security-7/ 
[6]:https://github.com/ptoomey3/Keychain-Dumper
[7]:https://code.google.com/p/iphone-dataprotection/wiki/KeychainViewer
[8]:http://wufawei.com/2013/06/Keychain-is-not-safe/ 
[9]:http://resources.infosecinstitute.com/ios-application-security-part-12-dumping-keychain-data/




























































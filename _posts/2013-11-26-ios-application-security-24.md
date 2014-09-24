---
layout: post  
title: iOS应用程序安全(24)-越狱检测与绕过  
categories:  
- iOS  
tags:    
- iOS Security
---   

作者：Prateek Gianchandani  
译者：吴发伟   
版权声明：自由转载-非商用-保持署名


本文我们将查看开发者在其应用中集成的、对运行其应用的设备是否越狱的检测方法。对你的应用来说，检测设备是否越狱有许多好处。我们已经知道，攻击者能够运行Cycript,GDB,Snoop-it等工具来执行运行时分析和窃取你的应用的敏感信息。 如果你确实想要为你的应用添加附加的安全层，你不应该允许你的应用在越狱设备上运行。请注意数百万的用户越狱了他们的设备，因此不让你的应用在越狱设备上运行可能会对你的用户基数造成较大的影响。你能做的另一件事就是禁用某些功能而不是整个应用。我们也将看看攻击者如何用Cycript来绕过应用内部的越狱检测。


设备越狱之后，有许多文件和应用被安装到设备上。检测文件系统中的这些文件可以帮助我们确定这个设备越狱与否。例如，大多数越狱设备都会在越狱之后安装Cydia。因此，简单的检查下Cydia的文件路径就能判断设备越狱与否。



	NSString *filePath = @"/Applications/Cydia.app";
	if ([[NSFileManager defaultManager] fileExistsAtPath:filePath])
	{
	   //Device is jailbroken
	}


不过，并不是所有的越狱设备都会安装Cydia。事实上，多数攻击者能够改变Cydia应用的位置。检查更多和越狱相关的其他文件能够让越狱检测更有效。例如，可以检查Mobile Substrate是否安装，许多越狱设备上的应用依赖它。也可以检查SSH Daemon的位置，或者shell解释器。把这些检查组合到一起，我们得到如下的方法。


	 +(BOOL)isJailbroken{
	 if ([[NSFileManager defaultManager] fileExistsAtPath:@"/Applications/Cydia.app"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/Library/MobileSubstrate/MobileSubstrate.dylib"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/bin/bash"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/usr/sbin/sshd"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/etc/apt"]){
	 return YES;
	 }
	 return NO;
	 }


从前面的系列文章中我们知道，以mobile用户运行的应用运行在沙盒环境中，位于/var/mobile/Applications目录，而以root用户运行的应用（比如苹果预加载的应用）并不属于任何沙盒环境，位于/Applications目录。运行越狱设备的用户可以把应用安装在/Applications目录，因此有root权限。因此，增加一个检测来看应用是否遵守沙盒规则能够让用户证实设备越狱与否。一个好方法就是检查看看我们是否能够修改应用bundle之外的文件。



	 NSError *error;
	 NSString *stringToBeWritten = @"This is a test.";
	 [stringToBeWritten writeToFile:@"/private/jailbreak.txt" atomically:YES
	 encoding:NSUTF8StringEncoding error:&error];
	 if(error==nil){
	 //Device is jailbroken
	 return YES;
	 } else {
	 //Device is not jailbroken
	 [[NSFileManager defaultManager] removeItemAtPath:@"/private/jailbreak.txt" error:nil];
	 }


我们知道技艺熟练的攻击者能够修改应用的位置。不过，我们知道超过80%的越狱设备会安装Cydia，即使攻击者能够改变Cydia应用的位置，他很可能也不会去改变Cydia应用注册的URL scheme。如果我们从应用中调用Cydia的URL scheme(cydia://）并且返回成功，那么我们可以确定设备是越狱了的。


	 if([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@"cydia://package/com.example.package"]]){
	 //Device is jailbroken
	 }


让我们加一个条件使得这个检查代码只在真是设备上执行，在模拟器上不执行。在组合上述技巧之后，我们的方法看起来像下面这样。


	 +(BOOL)isJailbroken{
	 #if !(TARGET_IPHONE_SIMULATOR)
	 if ([[NSFileManager defaultManager] fileExistsAtPath:@"/Applications/Cydia.app"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/Library/MobileSubstrate/MobileSubstrate.dylib"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/bin/bash"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/usr/sbin/sshd"]){
	 return YES;
	 }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/etc/apt"]){
	 return YES;
    }
	 NSError *error;
	 NSString *stringToBeWritten = @"This is a test.";
	 [stringToBeWritten writeToFile:@"/private/jailbreak.txt" atomically:YES
	 encoding:NSUTF8StringEncoding error:&error];
	 if(error==nil){
	 //Device is jailbroken
	 return YES;
	 } else {
	 [[NSFileManager defaultManager] removeItemAtPath:@"/private/jailbreak.txt" error:nil];
	 }
	 if([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@"cydia://package/com.example.package"]]){
	 //Device is jailbroken
	 return YES;
	 }
	 #endif
	 //All checks have failed. Most probably, the device is not jailbroken
	 return NO;
    }


老实说，没有万无一失的检测越狱设备的方法。技艺熟练的攻击者总是能够找到绕过的方法。攻击者能够从二进制文件中找到对应的指令，RNA好用No-op（译者注：汇编语言NOP，机器指令0x90）替换这些指令。攻击者也能够通过Cycript进行method swizzle来替换成他自己的实现。

通过使用Class-dump-z，攻击者能够找到应用的类信息。攻击者能够在JailbreakDetector类中找到方法 + (BOOL)isJailbroken。请注意它是一个类方法，因为它以＋开头。很显然这个方法检测设备是否越狱，如果越狱，就返回YES。如果你不理解这里说的这些，你应该先读读[前面的文章][1]。


![](http://resources.infosecinstitute.com/wp-content/uploads/112513_1713_IOSApplicat1.png)


攻击者能够用Cycript挂钩进这个应用。

![](http://resources.infosecinstitute.com/wp-content/uploads/112513_1713_IOSApplicat2.png)


然后打印出JailbreakDetector这个类的所有方法。请注意我们使用JailbreakDetector->isa.messages，因为isJailbroken是一个类方法。要找出实例方法，使用JailbreakDetector.messages即可。


![](http://resources.infosecinstitute.com/wp-content/uploads/112513_1713_IOSApplicat3.png)


然后攻击者就能够用他自己的始终返回NO的方法来替换这个方法（isJailbroken）的实现。如果你不理解这些，我建议你读读关于[Method Swizzling][2]这篇文章。


![](http://resources.infosecinstitute.com/wp-content/uploads/112513_1713_IOSApplicat4.png)


作为开发者，我们能做的就是改变这个方法的名字为一个不那么吸引攻击者注意力的名字。例如JailbreakDetector这个类名可以重命名为ColorAdditions， +(BOOL)isJailbroken 可以替换为+(BOOL)didChangeColor，而其实现不改变。这些名字不会吸引攻击者的注意。攻击者总是能够通过Snoop-it，GDB等工具来查看内部的方法调用，不过，像刚刚那样的一个小改动就能迷惑住攻击者。




[1]:http://wufawei.com/2013/11/ios-application-security-summary/ 

[2]:http://wufawei.com/2013/11/ios-application-security-8/ 
[3]:http://resources.infosecinstitute.com/ios-application-security-part-23-jailbreak-detection-evasion/ 





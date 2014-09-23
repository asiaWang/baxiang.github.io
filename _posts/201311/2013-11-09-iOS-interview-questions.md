---
layout: post  
title: iOS Interview Questions    
categories:  
- iOS  
tags:    
- iOS
---   
 
 [iOS Interview Questions][1] 列出很很多iOS相关的面试问题，把这些问题过一遍，有助于深化对iOS的理解。

* Explain method swizzling. When you would use it?，参见：[Monkey Patching iOS with Swizzling][2]

* What happens when you invoke a method on a nil pointer? [Calling a method on an unitialized object (null pointer)][3] 

*  Give two separate and independent reasons why retainCount should never be used in shipping code。[You should never use -retainCount, because it never tells you anything useful.][4]

* Explain your process for tracing and fixing a memory leak。[Instruments Tutorial for iOS: How To Debug Memory Leaks][5]

* Explain how an autorelease pool works at the runtime level. [Life-Before-ARC(2)][6]

* What is posing in Objective-C? [ClassPosing][7]

* [What are the differences between copy and retain][8]
* [Autoreleasing twice an object][9]

* [List the five iOS app states][10]
* Which is faster: to iterate through an NSArray or an NSSet [when is it better to use a NSSet rather than a NSArray][11]

* Explain how code signing works. [iOS Code Signing: Under The Hood][12] 、 [Demystifying iOS certificates and provisioning files][13]



[1]: http://www.raywenderlich.com/53962/ios-interview-questions
[2]: http://wufawei.com/2013/06/Monkey-Patching-iOS-with-Swizzling/
[3]: http://stackoverflow.com/questions/2696891/calling-a-method-on-an-unitialized-object-null-pointer
[4]: http://stackoverflow.com/questions/4636146/when-to-use-retaincount
[5]: http://www.raywenderlich.com/2696/
[6]: http://wufawei.com/2013/05/Life-Before-ARC%282%29/
[7]: http://cocoadev.com/ClassPosing 
[8]: http://stackoverflow.com/questions/2399490/what-is-the-difference-between-copy-and-retain
[9]: http://stackoverflow.com/questions/11291801/autoreleasing-twice-an-object
[10]: https://developer.apple.com/library/ios/documentation/iphone/conceptual/iphoneosprogrammingguide/ManagingYourApplicationsFlow/ManagingYourApplicationsFlow.html
[11]: http://stackoverflow.com/questions/10997404/when-is-it-better-to-use-a-nsset-rather-than-a-nsarray
[12]: http://www.raywenderlich.com/2915/ios-code-signing-under-the-hood
[13]: http://escoz.com/blog/demystifying-ios-certificates-and-provisioning-files/
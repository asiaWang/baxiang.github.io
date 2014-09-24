---
layout: post  
title: 本周看过文章汇总   
categories:  
- summary  
tags:    
- summary
---   
 
这里列出本周看过的我觉得有意思的文章和摘要：


1)[解密中国互联网](http://hi.baidu.com/ncaoz/item/6895b089a6cc71ded1f8cd4e) 
 
2)[Friday Q&A 2013-10-25: NSObject: the Class and the Protocol](http://www.mikeash.com/pyblog/friday-qa-2013-10-25-nsobject-the-class-and-the-protocol.html)

3) [Should I Use a Property or an Instance Variable?](http://blog.bignerdranch.com/4005-should-i-use-a-property-or-an-instance-variable/ )  
use Instance Variable is faster.

4)[Fast Image Cache by Path.Inc](https://path.com/blog/64726266082/fast-image-cache-on-github)

5) [How We Built Messaging](https://path.com/blog/57450126385/how-we-built-messaging )

6）Toll Free Bridging in [Friday Q&A 2011-09-30: Automatic Reference Counting](http://www.mikeash.com/pyblog/friday-qa-2011-09-30-automatic-reference-counting.html)

__bridge simply transfers a pointer between ARC and non-ARC with no transfer of ownership.

    CFStringRef valueCF = CFPreferencesCopyAppValue(CFSTR("someKey"), CFSTR("com.company.someapp"));  
    NSString *value = (__bridge NSString *)valueCF; 
    CFRelease(valueCF);
   

__bridge_transfer moves a non-Objective-C pointer to Objective-C and also transfers ownership, such that ARC will release the value for you.

    NSString *value = (__bridge_transfer NSString *)CFPreferencesCopyAppValue(CFSTR("someKey"), CFSTR("com.company.someapp")); 

    [self useValue: value];


__bridge_retained moves an Objective-C pointer to a non-Objective-C pointer and also transfers ownership, such that you, the programmer, are responsible for later calling CFRelease or otherwise releasing ownership of the object.

   CFStringRef value = (__bridge_retained CFStringRef)[self someString];
    UseCFStringValue(value); 

    CFRelease(value);
<br/>
7) [iOS 6 Push the limits](http://www.amazon.com/iOS-Programming-Pushing-Limits-Application/dp/1118449959)

 to avoid retain cycle,  prior to ARC, use __block,  after ARC, should use __weak or __unsafe_unretained, because  __block is retained (in ARC) not  copied (in non-ARC)

<br/>
8) [Customizing Navigation Bar and Status Bar in iOS 7](http://www.appcoda.com/customize-navigation-status-bar-ios-7/)

9)  [doing the impossible things in LinkedIn](http://engineering.linkedin.com/mobile/linkedin-intro-doing-impossible-ios)



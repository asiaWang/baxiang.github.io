---
layout: post 
title: iOS应用程序安全(26)- 使用IDA Pro给iOS应用打补丁  
categories: 
- iOS 
tags:   
- iOS Security
---  


作者：Prateek Gianchandani  
译者：吴发伟  
原文网址：http://resources.infosecinstitute.com/ios-application-security-part-26-patching-ios-applications-using-ida-pro-hex-fiend/ 
版权声明：自由转载-非商用-保持署名
 
 
 
前面的文章我们介绍了如何使用Cycript和GDB来修改程序的执行流程，不过用这些工具有个繁琐的地方就是每次启动后你都要做重复的工作。
本文将介绍如何给应用打补丁。这样你就不用每次都在运行时修改应用程序。

本文使用的一个工具就是[IDA Pro][1]，IDA Pro是一个非常强大的静态反汇编工具，甚至可以用插件F5一下就得到类似C语言的伪代码，当然，IDA Pro和这个插件是需要买的，不过网站上有Demo版本，有兴趣的可以下载试试。

在程序中要判断输入的密码这个字符串是否正确，总有一个地方是和正确的做比较。比如这样：
<pre><code>  if (input == PASSWORD) {
   // 输入正确
   } else {
  // 输入错误
  }
</code></pre>


从下图可以看到，只要让应用跳到loc_2CB2执行即可。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/121513_1608_IOSApplicat9.png )



可以把jnz loc_2CBC, 给改称jz loc_2CBC, 那么，输入任意错误的密码，就会走原来正确的流程。

归结到机器码，就是把0x85改成0x84。([Difference between JZ/JE, and JNE/JNZ][3])

具体如何修改，请参见原文:[IOS Application Security Part 26 – Patching IOS Applications using IDA Pro and Hex Fiend][2]


<br>
备注：
如果对使用IDA Pro感兴趣，推荐看看这本书 [IDA Pro权威指南][4]，英文原版在[这][5]


[1]:https://hex-rays.com/products/ida/index.shtml 
[2]:http://resources.infosecinstitute.com/ios-application-security-part-26-patching-ios-applications-using-ida-pro-hex-fiend/ 
[3]:http://stackoverflow.com/questions/14267081/difference-between-jz-je-and-jne-jnz
[4]:http://book.douban.com/subject/4311436/
[5]:http://www.idabook.com/index.html











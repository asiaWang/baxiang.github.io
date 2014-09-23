---
layout: post
title: 使用ASIHttpRequest发送数据
tags: object-c
category: object-c
---

1.设置请求头

{% highlight objective-c linenos%}
	
	ASIHTTPRequest *request = [ASIHTTPRequest request requestWithURL:url];
	[request addRequestHeader:@"Referer" value:@"http://githuo.com"];
	
{% endhighlight %}

2.Sending a form POST with ASIFormDataRequest


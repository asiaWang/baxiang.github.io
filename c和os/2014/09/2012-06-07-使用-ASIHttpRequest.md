---
layout: post
title: 使用ASIHttpRequest
tags: object-c
category: object-c
---

###创建和运行请求
1.创建一个同步请求
	
* 使用ASIHTTPRequest最简单的方法就是，我们发送startSynchronous的信息将会在同一条线程中执行，当请求完成之后，它会返回控制。
	
* 通过检查错误属性来检测问题
	
* 使用responseString方法来获取返回的一条字符串。使用responseData来获取NSData对象。如果是比较大的文件，给你的request请求设置downloadDestinationPath属性来下载文件。
	
代码如下：

{% highlight objective-c linenos %}
	
	- (IBAcation) grabURL:(id)sender
	{
		NSURL *url = [NSURL URLWithString:@"http://githuo.com"];
		ASIHTTPRequest *request = [ASIHTTPRequest requestWithURL:url];
		[request startSynchronous];
		NSError *error =[request error];
		if(!error){
			NSString *response =[request responseString];
		}
		
	}

{% endhighlight %}

    注意：
    	一般情况下，我们应该优先使用异步请求，因为当你我们使用来自主线程的ASIHTTPRequest同步请求时，
    	我们的应用界面就会被锁定并且导致在请求期间无法使用app。简单地说就是界面卡住，无法继续操作。
    	
    	
  <br><br>
 
 2.创建一个异步请求
  和以前的例子一样，只是这个请求是运行在多线程中的。
    	
{% highlight objective-c linenos %}
	
	-(IBAction)grabURLInBackground:(id)sender
	{
		NSURL *url = [NSURL URLWithString:@"http://githuo.com"];
		ASIHTTPRequest *request= [ASIHttpRequest requestWithURL:url];
		[request setDelegate:self];		
		[request startAsynchronous];
	}
	
	-(void)requestFinished:(ASIHTTPReuqest *) request
	{
		NSString *responseString = [request responseString];
	    NSData *responseData = [request responseData];
	}
	
	-(void)requestFailed:(ASIHTTPRequest *)request
	{
  		 NSError *error = [request error];
	}
 
{% endhighlight %}

		注意：
			我们为request请求设置了delegate,所以当请求成功或者请求失败之后，我们都可以收到通知。


<br><br>
3.使用blocks
 在1.8版本以后，我们可以在支持Blocks的平台上，使用blocks做同样的事情，		 
 {% highlight objective-c linenos %}
 

	- (IBAction)grabURLInBackground:(id)sender {
	
		NSURL *url = [NSURL URLWithString:@"http://githuo.com"];
		__block ASIHTTPRequest *request = [ASIHTTPRequest requestWithURL:url];

		[request setCompletionBlock:^{
			 NSString *responseString = [request responseString];			 NSData   *responseData	   = [request responseData];
		}];
		
		[request setFailedBlock:^{
			NSError *error = [request error];	
		}];
		
		[request startAsynchronous];

	}

{% endhighlight %}

	注意：
	  在我们定义request请求的时候，使用__block修饰符，这是非常重要的。这个告诉block不要增加request的引用计数（就是不要reatin）
	  这主要是为了防止循环引用。
		

4.使用queue
 
 这次我们为request请求，创建一个线程池NSOPerationQueue.
 
   - 使用NSOperationQueue(或者是 ASINetworkQueue)，当使用线程池的时候，只有一定数量的请求可以同时执行。
     如果你添加的请求数大于线程池的最大限制，多出来的请求就会等待其他请求结束之后，再开始发送自己。
     
 {% highlight objective-c linenos %}
	
	-(IBAction)grabURLInTheBackground:(id)sender
	{
		if(![self queue]){
			[self setQueue:[[NSOperationQueue alloc]init]autorelease];
		}
		NSURL *url = [NSURL URLWithString:@"http:githuo.com"];
		ASIHTTPRequest *request = [AIHTTPRequest requestWithURL:url];
		[request setDelegate:self];
		[request setDidFinishSelector:@selector(requestDone:)];
		[request setDidFailSelector:@selector(requestWentWrong:)];
		[[self queue]addOperation:request];
		
	}

	-(void)requestDone:(ASIHTTPRequest *)request
	{
		NSString *response = [request responseString];
	}
	
	-(void)requestWentWrong:(ASIHTTPRequest *)request
	{
		NSError *error = [requet error];
	}
	
 {% endhighlight %}

我们设置了自定义方法以便在请求成功或者在请求失败的时候调用。如果我们不自定义这些方法，
那么系统就会自动调用（requestFinished:和requestFailded:）,就像前面的例子一样。 



5.取消异步请求

- 取消一个异步请求的语句是[request cancel].同步请求不能被取消。
- 当你撤销掉一个请求之后，这个请求就会将它当做一个错误。然后调用你的delegate或者线程池失败的代理方法。如果你不想
  这样的话，可以在调用cancel之前设置你的代理方法为nil，或者使用clearDelegatesAndCancel方法来代替。
   
 {% highlight objective-c linenos %}
	
	[request cancel];	//撤销一个异步请求
	
	[request clearDelegatesAndCancel];	   //撤销一个异步请求，清除所有代理方法和blocks

 {% endhighlight %}









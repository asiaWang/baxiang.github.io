---
layout: post
title: "使用ImageIO.framework获取gif每一帧"
date: 2014-03-11 15:17:25 +0800
comments: true
categories: ios
---

---

>首先先简单介绍一下gif的几个算是术语吧：

1.frame（帧）：一个gif可以简单认为是多张image组成的动画，一帧就是其中一张图片image.

2.frameCount（帧数）： 就是一个gif有多少帧

3.loopCount（播放次数）：有些gif播放到一定次数就停止了，如果为0就代表gif一直循环播放。

4.delayTime（延迟时间）：每一帧播放的时间，也就是说这帧显示到delayTime就转到下一帧。


#### 所以gif播放主要就是把每一帧image解析出来，然后每一帧显示它对应的delaytime，然后再显示下一张。如此循环下去。

<pre><code>-(void)decodeWithFilePath:(NSString *)filePath

{
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^() {

        NSData *data = [NSData dataWithContentsOfFile:self.path];

        [self decodeWithData:data];

    });

}

</code></pre>

<pre><code> -(void)decodeWithData:(NSData *)data
{
    CGImageSourceRef src = CGImageSourceCreateWithData((CFDataRef) data, NULL);
    if (src)
    {
        //获取gif的帧数
        NSUInteger frameCount = CGImageSourceGetCount(src);
        //获取GfiImage的基本数据
        NSDictionary *gifProperties = (NSDictionary *) CGImageSourceCopyProperties(src, NULL);
        if(gifProperties)
        {
            //由GfiImage的基本数据获取gif数据
            NSDictionary *gifDictionary =[gifProperties objectForKey:(NSString*)kCGImagePropertyGIFDictionary];
            //获取gif的播放次数
            NSUInteger loopCount = [[gifDictionary objectForKey:(NSString*)kCGImagePropertyGIFLoopCount] integerValue];
            for (NSUInteger i = 0; i < frameCount; i++)
            {
                 //得到每一帧的CGImage
                CGImageRef img = CGImageSourceCreateImageAtIndex(src, (size_t) i, NULL);
                if (img)
                {
                    //把CGImage转化为UIImage
                    UIImage *frameImage = [UIImage imageWithCGImage:img];
                    //获取每一帧的图片信息
                    NSDictionary *frameProperties = (NSDictionary *) CGImageSourceCopyPropertiesAtIndex(src, (size_t) i, NULL);
                    if (frameProperties)
                    {
                        //由每一帧的图片信息获取gif信息
                        NSDictionary *frameDictionary = [frameProperties objectForKey:(NSString*)kCGImagePropertyGIFDictionary];
                        //取出每一帧的delaytime
                        CGFloat delayTime = [[frameDictionary objectForKey:(NSString*)kCGImagePropertyGIFDelayTime] floatValue];

                       //TODO 这里可以实现边解码边回调播放或者把每一帧image和delayTime存储起来
                        CFRelease(frameProperties);
                    }
                    CGImageRelease(img);
                }
            }
            CFRelease(gifProperties);
        }
        CFRelease(src);
    }    
}

</code></pre>
 

参考: [http://www.cnblogs.com/vicstudio/p/3339944.html](http://www.cnblogs.com/vicstudio/p/3339944.html)
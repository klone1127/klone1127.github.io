---
layout: post
title: Malloc Stack 开启后打印大量 log 问题
category: 技术 
tags: iOS
keywords:
description:
---



> 最近使用手机调试公司老项目的时候，测一次发现手机内存不够一次，腾出2G一会儿就用完了，然后发现那个缓存往上飙的老厉害了。正式版上没看到这个问题，然后......就被排期到了今天。



***因为项目中集成了一些第三方SDK，使用模拟器报错，还得把相关代码注释了才能运行，比较麻烦......只能使用真机解决这个问题。***



* 1 、使用 Instruments 分析，但分析了一番后也没发现哪里不对劲。
* 2、脱离 Xcode 测试，想到正式版并没有这个问题，然后就弄了个开发版装手机上运行测试，缓存显示一直没有什么大的变化

> 这么看来问题只有在连上 Xcode 测试的时候才会出现，那就是 Xcode 的配置问题了吧，

* 3、 使用 Xcode 运行，查看了 Xcode 性能监控提示发现…磁盘显示...惊人

![性能显示](http://olnx7jkmx.bkt.clouddn.com/Xcode-%E6%80%A7%E8%83%BD%E6%98%BE%E7%A4%BA.png?imageView2/0/interlace/1/q/100\|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10)

点进去看了发现：

![写文件](http://olnx7jkmx.bkt.clouddn.com/Malloc-Stack-logs-File.png?imageView2/0/interlace/1/q/100\|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10)

问题就是出在这儿了，一直有 log 在写入，那这个文件属于谁呢，在debug时有时需要打开 zombie 调试和 logging 调试，以帮助我们定位 bug，好了，到此已经算是解决问题了



> #### Edit Scheme -> Run -> Diagnostics -> 去掉打钩 Malloc Stack

去掉之后就会解决这个问题了，其实在控制器也会提示打印这个 log 。

![取消选择](http://olnx7jkmx.bkt.clouddn.com/noselect-Malloc-Stack.png?imageView2/0/interlace/1/q/100\|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10)
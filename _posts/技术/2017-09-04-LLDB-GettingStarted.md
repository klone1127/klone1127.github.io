---
layout: post
title: Chapter 1- Getting Started
category: 技术
tags: LLDB
keywords:
description:
---


> > 以下内容整理自：[<ADVANCED APPLE DEBUGGING & REVERSE ENGINEERING>](https://store.raywenderlich.com/products/advanced-apple-debugging-and-reverse-engineering) 如有出入望指正

> 由于OSX EI Capitan(10.11)开始引入了[***SIP(System Integrity Protection/系统完整性保护机制)***](http://devstreaming.apple.com/videos/wwdc/2015/706nu20qkag/706/706_security_and_your_apps.pdf)， 所以首先关闭SIP

* 重启电脑
* 开机按 Command + R，此时进入Recovery 模式
* 点击Utilities(工具),选择 Terminal，并输入

```shell
   csrutil disable; reboot
```
关闭SIP，
要开启SIP，使用
```shell
   csrutil endable; reboot
```

Terminal 中输入 lldb，

```shell
    lldb -n Finder
```

输出类似：

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-04-lldb_-n_Finder?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />

## Attaching LLDB to Xcode
> 使用 LLDB 调试 Xcode

打开一个新的终端，然后， ⌘ + Shift + I ，更改标签页标题为 LLDB

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-04-LLDB-Window?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />

确保没有 Xcode 正在运行，以防运行多个混淆
使用 ⌘ + T 新开一个， ⌘ + Shift + I ， 命名为Xcode stderr

	~$ tty

获取到当前窗口为

	/dev/ttys003

这个可能会不同
新开一个窗口，输入

	echo "hello debugger" 1>/dev/ttys003

这时在 ___Xcode stderr___ 中可以看到 hello debugger 打印出来了，关闭最后打开的窗口

切换到 LLDB 窗口，输入 lldb

	(lldb) file /Users/mylove/Downloads/Xcode-beta.app/Contents/MacOS/Xcode

我使用的是Xcode 9 beta 版，正式版可能是

	(lldb) file /Applications/Xcode.app/Contents/MacOS/Xcode

也可以使用 	

	ps -ef `pgrep -x Xcode`
查找

然后在LLDB 窗口输入

	(lldb) process launch -e /dev/ttys003 --

这时你可以在 Xcode stderr 看到Xcode输出的log，Xcode 也会被打开
> 新建swift项目

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-04-new-project?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />

> 切换到Xcode，打开ViewController.swift
### Finding a class with a click

-[NSView hitTest:] 在点击view的时候会被触发，我们通过给此方法添加断点进行调试
切换到 LLDB 窗口，Ctrl + C 暂停 debugger

	(lldb) breakpoint set -n "-[NSView hitTest:]"
	Breakpoint 2: where = AppKit`-[NSView hitTest:], address = 0x00007fffcb22b92b

此时处于debugger状态，输入continue

	(lldb) continue
	Process 13166 resuming

点击Xcode，任意位置

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-04-LLDB-debuggerXcode-continue?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />

使用 RDI 进行输出

	(lldb) po $rdi
	<_NSThemeCloseWidget: 0x128a1e9b0>

hitTest: 方法被触发，点击的位置不同，得到的结果或许不同

### Filter breakpoints for important content
给断点添加过滤条件，只有属于 NSTextView 类才会被触发

	(lldb) breakpoint modify 2 -c "(BOOL)[$rdi isKindOfClass:[NSTextView class]]"

其中，2是要修改的断点，可以通过以下方法查看

	(lldb) breakpoint list
	Current breakpoints:
	2: name = '-[NSView hitTest:]', locations = 1, resolved = 1, hit count = 1
  	2.1: where = AppKit`-[NSView hitTest:], address = 0x00007fffcb22b92b, resolved, hit count = 1 

因为我之前删了一个断点，所以现在的是2
之后就是点击Xcode，如果是 NSTextView 或者是其子类，都会响应。书中说是点击 code area in Xcode (代码区域)，断点会停下来，但是我点击后也没有停下来(Xcode 9 deta 6)，我是点击target version 区域

	(lldb) po $rdi
	<NSTextView: 0x13d18d960>
	Frame = {\{2.00, 3.00}, {266.00, 14.00}\}, Bounds = {\{0.00, 0.00}, {266.00, 14.00}\}
	Horizontally resizable: YES, Vertically resizable: YES
	MinSize = {266.00, 14.00}, MaxSize = {40000.00, 40000.00}

输出值
	(lldb) po [$rdi string]
	1.0

以上都是使用 Objective-C， 下面来确认下在 swift 中也可以使用，导入Foundation，AppKit

	(lldb) ex -l swift -- import Foundation 
	(lldb) ex -l swift -- import AppKit

ex 是 expression 的缩写，-l swift 告诉 LLDB 这是 swift 代码，通过替换之前的内存地址

	(lldb) ex -l swift -o -- unsafeBitCast(0x13d18d960, to: NSObject.self)
	(lldb) ex -l swift -o -- unsafeBitCast(0x13d18d960, to: NSObject.self) is NSTextView

___得到的结果和文中的有出入，稍后我会研究一下___

	(lldb) ex -l swift -o -- unsafeBitCast(0x13d18d960, to: NSObject.self)
	error: Couldn't lookup symbols:
	__TTSfq4n_n_d_d_n___TFs18_fatalErrorMessageFTVs12StaticStringS_4fileS_4lineSu5flagsVs6UInt32_Os5Never
	(lldb) ex -l swift -o -- unsafeBitCast(0x13d18d960, to: NSObject.self) is NSText
	error: Couldn't lookup symbols:
	__TTSfq4n_n_d_d_n___TFs18_fatalErrorMessageFTVs12StaticStringS_4fileS_4lineSu5flagsVs6UInt32_Os5Never

文中正确的结果，类似这种：

	(lldb) ex -l swift -o -- unsafeBitCast(0x14bdd9b50, NSObject.self) <NSTextViewSubclass: 0x14b7a65c0>
		Frame = {\{0.00, 0.00}, {1089.00, 1729.00}}, Bounds = {\{0.00, 0.00}, {1089.00, 1729.00}}
		Horizontally resizable: NO, Vertically resizable: YES
		MinSize = {1089.00, 259.00}, MaxSize = {10000000.00, 10000000.00}
	(lldb) ex -l swift -o -- unsafeBitCast(0x14bdd9b50, NSObject.self) is NSTextView
	true

使用 Swift 需要很多必要的条件，所以LLDB默认使用的是 Objective-C

更改version值

	po [$rdi setString:@"1.1"]
	po [CATransaction flush]

观察Xcode 中version 值得变化，此时变为1.1

### Hunting for private classes and methods in modules

	(lldb) image lookup -rn 'NSTextView\ '

从正在运行的二进制和加载的动态库中查找 NSTextView 相关class
>原文介绍

	This command lets you introspect the running binary and all loaded dynamic libraries. The r option instructs it to use a regular expression search. The n option instructs it to search functions or symbols by name.
	You’ll see a list of methods your NSTextView subclass implements!

### Swizzling with block injection

通过 runtime 机制进行方法替换

	(lldb) po @import Foundation

尽管Xcode中已经包含了这个库，但是 LLDB 并不知道识别不了，导入是为了在使用LLDB调试runtime 时使用
>现在输入

	(lldb) po
	Enter expressions, then terminate with an empty line to evaluate:
	1:  

输入以下代码：

	@import Cocoa; 
	id $class = [NSObject class]; 
	SEL $sel = @selector(init); 
	void *$method = (void *)class_getInstanceMethod($class, $sel); 
	IMP $oldImp = (IMP)method_getImplementation($method);

输入完就 enter(回车)

在 LLDB 中，所有变量都是使用 $ 作为开头

	(lldb) po $class
	NSObject
	(lldb) po $oldImp
	(libobjc.A.dylib`-[NSObject init])

使用 imp_implementationWithBlock 新建一个 IMP，使用 po 输入

	id (^$block)(id) = ^id(id object) { 
		if ((BOOL)[object isKindOfClass:[NSView class]]) {
	    	fprintf(stderr, "%s\n", (char *)[[[object class] description] UTF8String]); 
	    }
	    return object;
	}; 
	IMP $newImp = (IMP)imp_implementationWithBlock($block);
	method_setImplementation($method, $newImp);

此方法的目的是 swizzle -[NSObject init]， -[NSObject init]除了返回它自己，什么也不做，新建的block检测object是否属于 NSView 类，是的话就输出出来

文中解释其如何工作：	

	1. You create a block that takes an object reference.

	2. The block checks if the object passed in is of type NSView.

	3. If so, it prints a description of the view to stderr, which will appear on your Xcode stderr Terminal tab.

	4. It then returns object, to perform the equivalent implementation of -[NSObject init] that it’s swizzling. Ideally, swizzling these implementations would have been cleaner, and you could simply execute $oldImp with the right parameters. However, there is a bug in LLDB that will crash when executing IMPs inside of a block.

	5. Finally, a new IMP is created from the block, and the method implementation is set to this new IMP. This has therefore swizzled -[NSObject init] with your new implementation.

使用 continue 开始debugging。
在Xcode上点击，在Xcode stderr窗口中将会看到符合要求的输出

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-04-LLDB-blockSwizzleInit-View?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />

第一节就先这样，有不足之处再进行补充。

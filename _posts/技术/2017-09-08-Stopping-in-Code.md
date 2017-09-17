---
layout: post
title: Chapter 4 - Stopping in Code
category: 技术
tags: LLDB
keywords:
description:
---

> > 以下内容整理自：[<ADVANCED APPLE DEBUGGING & REVERSE ENGINEERING>](https://store.raywenderlich.com/products/advanced-apple-debugging-and-reverse-engineering) 如有出入望指正

> 不管你是使用 Swif, Objective-C, C++,C 或是其他语言，你都将学会使用断点。在图形界面的 Xcode 中点击面板边上就可以很轻松的设置一个断点，但是使用 LLDB 控制台可以带来更多控制方式的断点。

> [随书的代码资源下载](http://7xq3rg.com1.z0.glb.clouddn.com/AdvanceAppleDebugging_v0.9.1_SourceCode.zip)(我放七牛上的资源)，也可以[下载官网](https://wolverine.raywenderlich.com/print-books/dbg/AdvanceAppleDebugging_v0.9.1_SourceCode.zip)


### Signal

在这节中，可以使用随书的项目；名字是 __Signal__。__Signal__ 是一个 __basic master-detail__ 项目，这个项目可以几个 __Unix 信号__，并且可以在接收到的时候显示出来。

__Unix 信号__ 是系统中线程间通讯的一种有限制的方式。比如，有一个信号 __SIGSTOP__，用以保存状态、暂停进程执行，当收到 __SIGCONT__ 时，会让程序重新开始执行。这两个信号可以用于 __debugger__ 暂停 和继续运行程序。

这是程序在几个方面挺有趣的，因为它不仅探究 Unix 信号处理，而且当控制进程(LLDB)处理通过 Unix 信号传递到受控进程时可以高亮显示。默认情况下，LLDB 具有处理不同信号的自定义操作。在 LLDB attach 的时候，有些信号是不会受到控制。

想要显示一个信号，你可以在应用内 raise 一个信号，也可以从其他应用发送一个信号，如终端。

此外，有一个 UISwitch 触发信号处理，会调用 C 函数 sigprocmask 响应不可用或者可用信号处理。

最后，Signal 程序有一个 __Timeout__ 按钮可以在应用内唤起 SIGSTOP 信号，其实就是暂停程序。然而，如果 LLDB 已经 attach Signals(在你用 Xcode 编译运行的时候默认会的)，在 Xcode 中使用 LLDB 唤起 SIGSTOP 就可以检查执行程序状态。

>> 这几处原文

	Unix signals are a basic form of interprocess communication. For example, one of the signals, SIGSTOP, can be used to save the state and pause execution of a process, while its counterpart, SIGCONT, is sent to a program to resume execution. Both of these signals can be used by a debugger to pause and continue a program’s execution.

	This is an interesting application on several fronts, because it not only explores Unix signal handling, but also highlights what happens when a controlling process (LLDB) handles the passing of Unix signals to the controlled process. By default, LLDB has custom actions for handling different signals. Some signals are not passed onto the controlled process while LLDB is attached.

	In order to display a signal, you can either raise a Signal from within the application, or send a signal externally from a different application, like Terminal.

	In addition, there’s a UISwitch that toggles the signal handling blocking, which calls the C function sigprocmask to disable or enable the signal handlers.

	Finally, the Signal application has a Timeout bar button which raises the SIGSTOP signal from within the application, essentially “freezing” the program. However, if LLDB is attached to the Signals program (and by default it will be, when you build and run through Xcode), calling SIGSTOP will allow you to inspect the execution state with LLDB while in Xcode.

选择 __iPhone 7 Simulator__ 作为测试机，编译运行程序。程序运行后，点击暂停

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-15-Stopping-in-Code-Xcode?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="400px">

恢复运行，看一下模拟器。这时你会看到产生一条新的内容，这是被 Signals 监听的 Unix 信号被触发后添加到数据模型中的。当一个进程停止时，任何新的信号将不会被立即处理，因为程序在一定程度上停止了(the program is sort of, well, stopped.)。

### Xcode breakpoints

__Symbolic breakpoints__ 在 Xcode 中非常好的 debugging feature。在应用中可以添加包含 __Sympbol__ 的断点。举个  -[NSObject init] 的栗子，NSObject 实例的 init 方法。

比较优雅的事情是，在 Xcode 中添加一个symbolic breakpoint后，你在下次启动应用时不必重新输入。

现在可以使用 symbolic breakpoint 来显示所有正在创建的 NSObject 实例。

如果应用正在运行请关闭，切换到 __Breakpoint Naivgator(⌘ + 8)__，在左下方，点击 __+__，选择 __Symbolic Breakpoint...__。

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-15-symbolic-breakpoint?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="400px">

这时会有一个弹窗出现，在 __Symbol__ 中输入 __-[NSObject init]__。下边的 __Action__，选择 __ Add Action__ 然后下拉选择 __debugger Command__。

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-15-symbolic-breakpoint-set?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="400px">

编译运行，Xcode 将会打印所有运行时创建的类名，然后，，，会看到很多很多

你已经创建了一个每次运行 -[NSObject init] 都会被触发的断点。当断点触发，命令在 LLDB 种运行，程序继续执行。

   __Note__: 在第10章节可以知道 “Assembly, Registers and Calling Convention”，但现在，只要知道 __$arg1__ 和 __$rdi__ 作用相同就行，当 init 调用时，可以保存类的实例。

当你观察结束，可以symbolic breakpoint断点，对着断点右键，选择 __Delete Breakpoint__。

除了 symbolic breakpoint 之外，Xcode 还提供了其他的类型的断点，有个 __Exception Breakpoint__，程序出错的时候或者crash，这时设置一个 __Exception Breakpoint__，在程序抛出异常的时候会被触发。Xcode 会显示哪一行的问题，这极大的帮助了寻找crash的根源。

最后，是 __Swift Error Breakpoint__，在任何时候，Swift 都会通过在 swift_willThrow 方法创建一个断点来抛出后错误。如果你正在使用易于出错的API，这是一个很好的选择，因为它可以让你快速诊断出情况，而不会让你对正确的代码做出错误的假设。

### LLDB breakpoint syntax

现在你已经对Xcode的调试功能有了一定的了解，可以开始学习通过 LLDB 控制台创建断点了。为了创建有用的断点，你需要学习如何查询你想要查询的内容。

__image__ 命令是一个很好的工具，可以帮助内省细节，这对设置断点至关重要。

在书中你将会有两种用于代码搜索的配置。第一种就是以下方法:

	(lldb) image lookup -n  "-[UIViewController viewDidLoad]"

这个命令输出 -[UIViewController viewDidLoad] 加载的地址， -n 参数告诉 LLDB 查找符号或者函数名。类似输出：

	1 match found in 	/Users/mylove/Downloads/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/System/Library/Frameworks/UIKit.framework/UIKit:
	    Address: UIKit[0x00000000001c76db] (UIKit.__TEXT.__text + 1858059)
	    Summary: UIKit`-[UIViewController viewDidLoad]

另一个有用且类似的命令是：

	(lldb) image lookup -rn test

通过区分大小写的正则来查找 "test"。如果这个小写 "test 在任何位置，任何函数，任何模块（i.e. UIKit， Foundation， Core Data， etc）中加载到当前可执行文件（不会从 release builds中移除。。。稍后再说），这个命令会输出结果。

   __Note__: 当你需要精确匹配的时候，使用 -n 参数(如果包含空格，则可以使用引号包住)，并用 -rn 参数进行正则表达式搜索。-n 命令有助于匹配断点的确切参数，尤其是处理 Swift的时候，而 -rn 参数将会非常受到青睐，因为一个巧妙的正则表达式就可以避免打很多字。

### Objective-C properties

学习如何查询加载的代码对于学习如何在该代码上创建断点至关重要。Swift 和 Objective-C 的属性签名在它们被编译器创建已经生成了，这就导致了不同的断点策略。

举例来说，以下是 Objective-C 在 Signals 中声明的属性：

	@interface TestClass : NSObject 
	@property (nonatomic, strong) NSString *name;
	@end

编译器会为 name 生成 setter 和 getter 方法。
getter 方法：

	-[TestClass name]

setter 方法：

	-[TestClass setName:]

编译运行项目，然后暂定调试，通过 LLDB 验证方法是否存在:

	(lldb) image lookup -n "-[TestClass name]"
	1 match found in /Users/mylove/Library/Developer/Xcode/DerivedData/Signals-fdzrglclifcqooeqhnyaxxvmqakl/Build/Products/Debug-iphonesimulator/Signals.app/Signals:
	    Address: Signals[0x0000000100001cb0] (Signals.__TEXT.__text + 0)
	    Summary: Signals`-[TestClass name] at TestClass.h:28

LLDB 将输出包含在可执行文件中的函数信息。输出的信息看起来有点吓人，但这里有一些很好的信息。

控制台输出可以得出信息，LLDB 可以在 Signals 可执行文件中发现这个方法实现，在TEXT中的 0x0000000100001470偏移量是准确的，LLDB 也指明这个方法是在 TestClass.h 文件中的28行声明。

你也可以查找 setter 方法：

	(lldb) image lookup -n "-[TestClass setName:]"
	1 match found in /Users/mylove/Library/Developer/Xcode/DerivedData/Signals-fdzrglclifcqooeqhnyaxxvmqakl/Build/Products/Debug-iphonesimulator/Signals.app/Signals:
	    Address: Signals[0x0000000100001cd0] (Signals.__TEXT.__text + 32)
	    Summary: Signals`-[TestClass setName:] at TestClass.h:28

### Swift properties

属性在 Swift 中的语法就有很大不同了，可以看下 SwiftTestClass.swift:

	class SwiftTestClass: NSObject { 
	var name: String!
	}

确认 __Signals__ 是运行的，并且处于 LLDB 状态，__⌘ + k__ 清空控制台信息。

在 LLDB 控制台，输入：

	(lldb) image lookup -rn Signals.SwiftTestClass.name.setter

输出类似信息:

	2 matches found in /Users/mylove/Library/Developer/Xcode/DerivedData/Signals-fdzrglclifcqooeqhnyaxxvmqakl/Build/Products/Debug-iphonesimulator/Signals.app/Signals:
	    Address: Signals[0x000000010000caf0] (Signals.__TEXT.__text + 44608)
	    Summary: Signals`@objc Signals.SwiftTestClass.name.setter : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift        Address: Signals[0x000000010000cbb0] (Signals.__TEXT.__text + 44800)
	    Summary: Signals`Signals.SwiftTestClass.name.setter : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift:28

在输出内容中查找 __Summary__ 。这里是成对输出的。

首先，发现有两个 symbols。第一个和第二个同名；然而，第一个开头有个 @objc，这是一个特殊的函数是由编译器加上去的，作为 bridging 功能。有助于 Swift 和 Objective-C 混编。

> 未完待续


> ### 延伸阅读

[Unix 信号-维基百科](https://zh.wikipedia.org/wiki/Unix%E4%BF%A1%E5%8F%B7)

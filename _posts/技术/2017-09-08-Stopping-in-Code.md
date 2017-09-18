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

其次，你有没有发现函数名有多长？！设置一个Swift断点需要写出所有东西，！如果你想在 setter 上设置一个断点，你需要写以下类似内容：

	(lldb) b Signals.SwiftTestClass.name.setter : Swift.ImplicitlyUnwrappedOptional<Swift.String>

使用正则表达式是一个有吸引力的替代这种残暴输入的方法。

除了生成的Swift函数长度之外，注意Swift属性的生成。函数名后跟着属性name，之后跟着setter，也许这也适用于getter？

使用以下方法同时搜索 SwiftTestClass 属性name的 setter 和 getter 方法：

	(lldb) image lookup -rn Signals.SwiftTestClass.name.

使用正则表达式查找所有包含 Signals.SwiftTestClass.name. 的内容。

输出类似以下内容：

	6 matches found in /Users/mylove/Library/Developer/Xcode/DerivedData/Signals-fdzrglclifcqooeqhnyaxxvmqakl/Build/Products/Debug-iphonesimulator/Signals.app/Signals:
	    Address: Signals[0x000000010000c980] (Signals.__TEXT.__text + 44240)
	    Summary: Signals`@objc Signals.SwiftTestClass.name.getter : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift        Address: Signals[0x000000010000ca40] (Signals.__TEXT.__text + 44432)
	    Summary: Signals`Signals.SwiftTestClass.name.getter : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift:28        Address: Signals[0x000000010000caf0] (Signals.__TEXT.__text + 44608)
	    Summary: Signals`@objc Signals.SwiftTestClass.name.setter : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift        Address: Signals[0x000000010000cbb0] (Signals.__TEXT.__text + 44800)
	    Summary: Signals`Signals.SwiftTestClass.name.setter : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift:28        Address: Signals[0x000000010000ccc0] (Signals.__TEXT.__text + 45072)
	    Summary: Signals`closure #1 : () in Signals.SwiftTestClass.name.materializeForSet : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift        Address: Signals[0x000000010000ccd0] (Signals.__TEXT.__text + 45088)
	    Summary: Signals`Signals.SwiftTestClass.name.materializeForSet : Swift.ImplicitlyUnwrappedOptional<Swift.String> at SwiftTestClass.swift

重点看 __Summary__ ，你可以看到 setter 和 getter 方法，materializeForSet 方法。

这里有一个Swift属性模板：

	ModuleName.ClassName.PropertyName.(getter|setter)

### Finally... creating breakpoints

现在你已经知道在怎么在代码中查询函数和方法是否存在了，是时候开始给它们下断点了。

创建断点的方法有几种。__b__ 是最基本的一种。这在Objective-C和C中相当容易，因为名字很短，容易输入(e.g. -[NSObject init])。这在C++和Swift中是相当棘手的，因为编译器将你的方法转成名字很长的符号。

由于UIKit主要由Objective-C编写(至少书中原文撰写的时候)，尝试使用 b 创建一个断点，像下面这样：

	(lldb) b -[UIViewController viewDidLoad]

类似这样的输出：

	Breakpoint 4: where = UIKit`-[UIViewController viewDidLoad], address = 0x000000010ce426db

__Breakpoint 4:__ 说明我这是创建的第四个断点，多个的时候自增。

断点创建好了之后，继续运行Xcode，这时Signals上会增加一个新的cell，点击cell查看详情，刚设置的断点会在加载 viewDidLoad 方法的时候被触发。

__Note:__ 像很多简写命令一样，b 是另一个长的 LLDB 命令的缩写。尝试运行 help

	(lldb) help b
	 Set a breakpoint using one of several shorthand formats.
	 Expects 'raw' input (see 'help raw-input'.)
	
	Syntax: b
	
	'b' is an abbreviation for '_regexp-break'

除了 b 命令，还有一个有一些选项的 breakpoint set 命令。

### Regex breakpoints and scope

另一个非常强大的命令是正则表达式断点 __rbreak__，它是 __breakpoint set -r %1__ 的缩写。

回到上一个例子，Swift有很长的属性名：

	(lldb) b Signals.SwiftTestClass.name.setter : Swift.ImplicitlyUnwrappedOptional<Swift.String>
>这里需要指出，书中写的是：
	(lldb) b Breakpoints.SwiftTestClass.name.setter :
	Swift.ImplicitlyUnwrappedOptional<Swift.String>
使用书中的方法好像没什么作用。

简单设置断点：

	(lldb) rb SwiftTestClass.name.setter

尽管这个方法简单，但也是有点烦恼的。这个断点会捕获两个 setter，包括 Objective-C 桥接文件在内的方法。

你可以添加 ^(@).*  到断点上来扩充断点，基本上可以说“不用让函数以@开头了”，在以后的章节中，你将构建执行正则表达式的可以自动过滤掉桥接函数的命令。

现在你只需要处理两个断点。更简短，可以用下面的命令：

	(lldb) rb name\.setter

这个命令会产生一些包含 name.setter 的断点。如果你肯定你项目中没有其他名为name的属性，这将很有用；否则你将为它们创建多个断点。

现在尝试为所有Objective-C 的UIViewController 设置断点，在 LLDB 会话中执行：

	(lldb) rb '\-\[UIViewController\ '
	Breakpoint 5: 762 locations.

使用 __\__ 转义， 表示你希望文字在正则表达式中搜索。结果就是，所有包含'-[UIViewController '(添加''是为了指出此处有空格)的方法都设置了断点。

稍等...关于Objective-C 的扩展呢？它们的格式为 (-|+)[ClassName(categoryName) method]。你必须重写正则表达式以包括类别。

删除所有断点，输入以下内容，并在提示后直接 Enter/输入y：

	(lldb) breakpoint delete
	About to delete all breakpoints, do you want to do that?: [Y/n] 

下一步，输入：

	(lldb) rb '\-\[UIViewController(\(\w+\))?\ '
	Breakpoint 6: 928 locations.

在断点中UIViewController后可以有一个带有一个或多个字符的括号。

使用正则表达式断点，你可以使用单个表达式捕获各种各样的断点。

使用 -f 参数，可以将断点的范围限制在某一个文件中。例如，像下面这样：

	(lldb) rb . -f DetailViewController.swift

当你调试DetailViewController.swift的时候会被触发。它将在这个文件中的所有属性的getters/setters，block/closures，extensions/categories，和 函数/方法 上添加断点。-f 常被用作范围限制。

如果你想更疯狂的尝试，可以去掉范围限制：

	(lldb) rb .

它将在任何地方添加断点...包括Signals所有代码，使用到的UIKit和Foundation，所有run loop 事件代码，

你也可以使用__-s__限制在一个library中:

	(lldb) rb . Commons

这将在Commons库中的所有内容上设置一个断点，它是Signalals项目中包含的一个动态库。

你可以使用相同的方法对在UIKit上添加断点。

	(lldb) rb . -s UIKit

只停止在你点的第一个包含在UIKit中的方法上怎么样？__-o__ 参数可以解决，它创建了“one-shot”断点。断点会在被命中后删除。所以它只会命中一次。

	(lldb) breakpoint delete
	(lldb) rb . -s UIKit -o

__Note:__ 输入命令后需要等一会儿，因为这时需要设置很多很多断点。请确认你使用的是模拟器，否则你将等待更长时间。

下一步，继续调试，点击模拟器上的cell。这时停在触发的第一个UIKit的方法上，然后 continue，将不会再触发断点。

### Modifying and removing breakpoints

现在你已经基本可以创建断点了，你可能会想知道怎么更改。如果你想删除断点或者暂时禁用，怎么办？如果你需要修改断点以在下次触发时执行特定操作，该怎么办？

首先，你需要发现断点的唯一标示，或者一组断点。你可以使用 __-N__ 在创建时给断点命名...

输入：

	(lldb) b main
	Breakpoint 1: 44 locations.

这个命令会在44个位置创建断点，与各个模块中的 “main” 功能匹配。

breakpoint ID 是1，因为这是在这个会话中创建的第一个断点。要想看这个断点的详细信息可以输入：

	(lldb) breakpoint list 1

会得到类似输出：

	Current breakpoints:
	1: name = 'main', locations = 44, resolved = 44, hit count = 0
	  1.1: where = Signals`main + 4 at AppDelegate.swift:28, address = 0x0000000103ee8544, resolved, hit count = 0 
	  1.2: where = Foundation`-[NSThread main], address = 0x00000001042309e3, resolved, hit count = 0 
	  1.3: where = Foundation`-[NSBlockOperation main], address = 0x000000010423d7d6, resolved, hit count = 0 
	  1.4: where = Foundation`-[NSFilesystemItemRemoveOperation main], address = 0x0000000104276e99, resolved, hit count = 0 
	  1.5: where = Foundation`-[NSFilesystemItemMoveOperation main], address = 0x00000001042779ee, resolved, hit count = 0 
	...

更清晰的查看方法：

	(lldb) breakpoint list 1 -b
	1: name = 'main', locations = 44, resolved = 44, hit count = 0

这个输出简明扼要，如果你有大量断点，可以使用这个方法。

如果你想查看所有断点：

	(lldb) breakpoint list

也可以根据ID和范围查看：

	(lldb) breakpoint list 1 3
	(lldb) breakpoint list 1-3

可以单独删除一个断点：

>> 根据ID
	(lldb) breakpoint delete 1

>> 也可以根据位置进行删除

	(lldb) breakpoint delete 1.1

最后这个之后删除第一个断点的第一个子断点。

> ### 扩展阅读

* [Unix 信号-维基百科](https://zh.wikipedia.org/wiki/Unix%E4%BF%A1%E5%8F%B7)
* [python 正则表达式](https://docs.python.org/2/library/re.html)

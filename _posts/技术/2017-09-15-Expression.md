---
layout: post
title: Chapter 5 - Expression
category: 技术
tags: LLDB
keywords:
description:
---

> > 以下内容整理自：[<ADVANCED APPLE DEBUGGING & REVERSE ENGINEERING>](https://store.raywenderlich.com/products/advanced-apple-debugging-and-reverse-engineering) 如有出入望指正

> 本节主要讲通过 __expression__ 命令对代码进行调试

### Formatting p & po

__po__ 经常用于 Swift & Objective-C 的一些输出操作。

如果你使用 __help po__ 查看的话，你会发现 __po__ 是 __expression -O  --__ 的缩写，__O__ 用于输出对象的描述信息。

__po__ 的一个经常被忽略的姐妹，__p__，是一个省去了__O__ 的表达式（**expression -O  --**）。p 输出格式更多的依赖于 __LLDB type system__。LLDB 的值类型格式有助于确定其输出，并可以完全自定义(像你将在下面看到的那样)。

现在是时候学习 p&po 命令如何获取内容的时候了。这此节将继续使用 Signals 项目。

打开 __MasterViewController.swift__ ，添加如下代码：

	  override var description: String {
		return "Yay! debugging " + super.description
	  }

在 __viewDidLoad__ 方法中，添加代码到 __super.viewDidLoad()__ 下面：

	print("\(self)")

添加断点到 print 方法下边，像下面这样，编译运行：

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-09-15-Expression-add-breakpoint?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10">

停到 viewDidLoad() 之后，输入：

	(lldb) po self
	Yay! debugging <Signals.MasterViewController: 0x7f8335f0db10>

记下 print 语句的输出以及它如何匹配的你刚刚在调试器中执行的 po self。

你也可以进一步，NSObject 有另一种方法没描述用于调试称为 __debugDescription__。现在试着实现。在刚添加 description 变量下面添加：

	  override var debugDescription: String {
	    return "debugDescription: " + super.debugDescription
	  }

编译运行，这时在断点处停下后输入：

	(lldb) po self

控制台输出与下面类似：

	debugDescription: Yay! debugging <Signals.MasterViewController: 0x7f9b27c0df80>

注意在你执行了 debugDescription 之后，po self 和 print 输出的 self 的不同。使用 LLDB 打印对象的时候，调用 debugDescription 方法，而不是 description。

你可以看到，在使用 NSObject 类或者子类的时，`description` 或者 `debugDescription` 将影响 `po` 的输出。

那么哪些对象重写了这些描述方法？可以使用 `image lookup` 查看。

举例来说，如果你想知道所有重写 `debugDescription` 的  Objective-C 的类，可以使用：

	(lldb) image lookup -rn '\ debugDescription\]'

基于输出，Foundation 框架的开发者将 `debugDescription` 添加到了很多基础类中(i.e. NSArray)，这样方面于我们调试。此外，也有私有类重写了 `debugDescription` 方法。

在输出中可以看到有 CALayer。让我们看看 `description` 和 `debugDescription` 在 CALayer 中的不同。

输入：

	(lldb) po self.view.layer.description

输出类似信息：

	"<CALayer: 0x600000034f60>"

信息太少了，换个方式：

	(lldb) po self.view.layer

得到类似以下输出：

	<CALayer:0x600000034f60; position = CGPoint (187.5 333.5); bounds = CGRect (0 0; 375 667); delegate = <UITableView: 0x7fc2d501a000; frame = (0 0; 375 667); clipsToBounds = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x600000244b90>; layer = <CALayer: 0x600000034f60>; contentOffset: {0, 0}; contentSize: {600, 0}; adjustedContentInset: {0, 0, 0, 0}>; sublayers = (<CALayer: 0x600000035480>, <CALayer: 0x600000035520>); masksToBounds = YES; allowsGroupOpacity = YES; backgroundColor = <CGColor 0x6000000b8d80> [<CGColorSpace 0x6000000b8780> (kCGColorSpaceICCBased; kCGColorSpaceModelRGB; sRGB IEC61966-2.1; extended range)] ( 0.980392 0.980392 0.980392 1 )>

输出很多很有用的信息，很显然， Core Animation 的开发人员决定 `description` 应该只是对象引用，但如果你在调试器中，你可以看到更多的信息。不清楚为什么要造成这种差异。可能是为了节省资源。

下一步，如果你仍停在调试器(没有的话，回到 viewDidLoad() 断点)，尝试在 `self` 上运行 `p` 命令

	(lldb) p self

得到类似以下输出：

	(Signals.MasterViewController) $R3 = 0x00007fc2d4c0b080 {
	  UIKit.UITableViewController = {
	    baseUIViewController@0 = <extracting data from value failed>
	
	    _tableViewStyle = 0
	    _keyboardSupport = nil
	    _staticDataSource = nil
	    _filteredDataSource = 0x0000608000247d70
	    _filteredDataType = 0
	  }
	  detailViewController = nil
	}

看起来有点恐怖，让我们慢慢讲。

首先，LLDB 输出 self 的类名。类名：Signals.MasterViewController

然后是一个引用，你可以在 LLDB 会话中引用这个对象。栗子中的是 `$R0`，这个数字会在你使用 LLDB 的时候增加。

如果你想在之后的会话中使用这个对象，或许当你处于不同的范围并且 `self` 不再是同一个对象，这个引用将非常有用。这种情况下，你可以使用 `$R0` 使用这个对象。使用方法：

	(lldb) p $R0

你可以看到同样信息又输出了一边。

在 LLDB 变量名称之后是该对象的地址，后面是一些特定于此类类型的输出。在这种情况下，它显示 MasterViewController 的父类 UITableViewController 的相关信息，后跟 detailViewController 实例变量。

p 的输出取决于 __type formatting__，LLDB 开发人员已经在内部数据结构中添加了所有的(受关注的)数据结构，Objective-C、 Swift 还有一些其他的语言。尤其要注意的是， Swift 处在开发阶段，所以 MasterViewController 输出可以有所不同。

由于这些类型类型格式化程序是由 LLDB 持有的，所以如果愿意，可以直接更改的。在 LLDB 会话中，输入：

	(lldb) type summary add Signals.MasterViewController --summary-string "哇喔"

你告诉 LLDB 当输出 MasterViewController 实例的时候你只想输出字符串 “哇喔”。输出 self：

	(lldb) p self
	(Signals.MasterViewController) $R0 = 0x00007fdd377029e0 哇喔

这个样式会被 LLDB 记录，下次启动的时候还存在，需要在使用结束之后清除掉。

	(lldb) type summary clear

现在输入 `p self` 就回到默认输出样式了。

### Swift vs Objective-C debugging contexts

需要注意的是调试程序时有两个调试上下文：非 Swift 调试上下文和 Swift 上下文。默认情况下，当停在 Objective-C 代码时，LLDB 使用非 Swift(Objective-C) 调试上下文，而在 Swift 代码中停止时， LLDB 将使用 Swift上下文。听起来很合逻辑，对吧？

如果你意外的停止，LLDB 将默认选择 Objective-C 上下文。

确保刚才的蓝色(GUI 下设置)断点还在，编译运行。停在断点处后，在LLDB 会话中输入：

	(lldb) po [UIApplication sharedApplication]

LLDB 会抛出一个奇怪的错误：

	error: <EXPR>:3:16: error: expected ',' separator
	[UIApplication sharedApplication]
	               ^
	              ,

你这时是停在 Swift 代码中，所以是 Swift 上下文，而且你执行的是 Objective-C 代码。因此行不通。同样的，在 Objective-C 上下文 po Swift 多象也是不可以的。

在 Objective-C 上下文时可以在 expression 中使用 `-l` 来选择语言。然而，由于 `po` 是 `expression -O  --` 的映射，所以你不能直接在 `po` 后面直接跟 `--`，这意味着需要使用 `expression`。在 LLDB，输入：

	(lldb) expression -l objc -O -- [UIApplication sharedApplication]

这时你告诉 LLDB 为 Objective-C 使用 `objc`，如果有需要，还可以为 Objective-C++ 使用 `objc++`。

使用 Swift 做同样的事情：

	(lldb) po UIApplication.shared

和使用 Objective-C 输出的相同，然后 continue，之后再突然停止。

按 `↑`，看到：

	(lldb) po UIApplication.shared

这时看输出：

	error: property 'shared' not found on object of type 'UIApplication'

请记住，LLDB 意外停止时使用的是 Objective-C 上下文，这就是为什么执行 Swift 方法出现错误的原因。

你应该始终了解当前在调试器中暂停的位置使用的语言。

### User defined variables

如之前看到的，当打印对象时，LLDB 将自动创建本地变量。你也可以创建自己的变量。

移除所有断点，运行应用。在调试器意外的停止，默认是 Objective-C 上下文。输入：

	(lldb) po id test = [NSObject new]

LLDB 将会执行这个代码，该代码创建一个新的 NSObject 并将其存储到 test 变量中。现在打印出来：

	(lldb) po $test

	出现一个错误：

	error: use of undeclared identifier 'test'

这是因为你需要使用 `$` 预先添加要使用 LLDB 的变量。

重新输入：

	(lldb) po id $test = [NSObject new]
	(lldb) po $test
	<NSObject: 0x604000014eb0>

此变量在 Objective-C 对象中创建。但是，如果你尝试从 Swift 上下文访问会发生什么？试一下：

	(lldb) expression -l swift -O -- $test

发现输出还是正常的。现在尝试使用 Swift 代码风格在这个 Objective-C 上执行。

	(lldb) expression -l swift -O -- $test.description

输出错误：

	error: <EXPR>:3:1: error: use of unresolved identifier '$test'
	$test.description
	^~~~~

如果在 Objective-C 上下文中创建一个 LLDB 变量，然后使用 Swift 上下文，不要指望一切可以“正常运行”。目前这块在积极开发，Objective-C 和 Swift 桥接可能会随着时间的推移而改善。

那么如何在 LLDB 中创建引用可以在实际情况下使用？你可以获取对象的引用，并执行(以及调试)任意选择的方法。要查看此操作，创建一个符号断点在 MasterViewController 的父控制器 __MasterContainerViewController__上，为 MasterContainerViewController 的 viewDidLoad 添加符号断点。

在 Symbol 行输入：

	Signals.MasterContainerViewController.viewDidLoad() -> ()

输完之后如下：

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-10-12-Symbolic-Breakpoint-MasterContainerViewController-viewDidLoad?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10">

编译运行，Xcode 将停在 MasterContainerViewController.viewDidLoad()。输入：

	(lldb) p self

由于这是在 Swift 上下文中执行的第一个参数，所以 LLDB 将会创建变量 `$R0`。通过在 LLDB 中执行 continue 恢复程序的执行。

现在你没有通过使用 self 来引用 MasterContainerViewController 的实例，因为执行已经不在 viewDidLoad()，并移动到 bigger and better run loop events。

你这时候还有 `$R0` 变量，你可以引用 MasterContainerViewController，甚至是执行任意方法来帮助调试代码。

手动暂定调试的应用，然后输入：

	(lldb) po $R0.title

很不幸，报错了：

	error: use of undeclared identifier '$R0'

因为这时候调试器是意外停止的。记住，LLDB 默认的是 Objective-C；你需要使用 `-l` 来保证使用的是 Swift 上下文；

	(lldb) expression -l swift -- $R0.title

这时候看下输出：

	(String?) $R1 = "Quarterback"

当然，这是 view Controller 的标题，如导航栏显示。

现在输入：

	(lldb) expression -l swift -- $R0.title = "😛😛😛😛"

然后 `continue` 恢复运行。

> __Note:__ 在 macOS 上使用 `⌘ + ⌃(control) + 空格` 可以进行符号和表情的选择。

<img src="http://olnx7jkmx.bkt.clouddn.com/2017-10-13-lldb-change-navigationItemTitle?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10">

此外，你也可以在代码中创建断点，执行代码，命中断点。如果你正在调试并希望通过某些输入来查看其操作方式，这很可能会有用。

例如：你符号断点仍然在 viewDidLoad() 方法上，请尝试执行该方法来检查代码。暂停执行程序，然后输入：

	(lldb) expression -l swift -O -- $R0.viewDidLoad()

什么也没有发生，断点也没有出现。是什么原因呢？实际上，MasterContainViewController 已经执行了这个方法。但是默认情况下，LLDB 会在执行命令是忽略任何断点。你可以使用 `-i` 禁用此选项。

在 LLDB 会话输入：

	(lldb) expression -l swift -O -i 0 -- $R0.viewDidLoad()

LLDB 现在在之前创建的 viewDidLoad() 符号断点中断。这种策略是测试逻辑的好方法。例如，你可以通过给出不同参数的函数来实现 test-driven 调试。以了解如何处理不同的输入。

### Type formatting

编译运行，然后暂停，确保你停在 Objective-C 上下文。

	(lldb) expression -G x -- 10

`-G` 告诉 __LLDB__ 输出想要使用的格式。__G__ 表示 __GDB__。GDB 是 LLDB 之前的调试器。所以，这时你指定了使用 GDB 格式。在这种情况下， `x` 表示十六进制。

输出结果：

	(int) $0 = 0x0000000a

这是十进制10的十六进制。 

还有更多！LLDB 允许你使用简洁语法格式。输入：

	(lldb) p/x 10
	(int) $1 = 0x0000000a

你会发现一样的输出。但是输入少了很多。

这对学习 C 数据类型很有帮助。例如，10的二进制数据?

	(lldb) p/t 10
	(int) $2 = 0b00000000000000000000000000001010

`/t` 指定了二进制格式。你可以看到10的二进制显示。例如：当处理一个位字段是很有用，以便自习检查给定数字将设置哪些字段。

-10 的二进制是多少？

	(lldb) p/t -10
	(int) $3 = 0b11111111111111111111111111110110

10.0的二进制呢？

	(lldb) p/t 10.0
	(double) $4 = 0b0100000000100100000000000000000000000000000000000000000000000000

'D' 的 __ASCII__ 是多少？

	(lldb) p/d 'D'
	(char) $5 = 68

`/d` 指定的是十进制格式。

最后，下面这个整数隐藏的缩写是什么？

	(lldb) p/c 1430672467
	(int) $7 = STFU

`/c` 指定的是字符串格式。它采用二进制数，分为8位(1 字节)，并将每个块转换为 **ASCII** 字符。在这种情况下，它是一个4字符的代码(FourCC),结果是 `SDFU`。

完整的输出格式列表([https://sourceware.org/gdb/ onlinedocs/gdb/Output-Formats.html](https://sourceware.org/gdb/ onlinedocs/gdb/Output-Formats.html))

* x: 十六进制(hexadecimal)
* d: 十进制(decimal)
* u: 无符号十进制(unsigned decimal)
* o: 八进制(octal)
* t: 二进制(binary)
* a: 地址(address)
* c: 字符常数(character constant)
* f: 浮点数(float)
* s: 字符串(string)

如果这些格式不够用，还可以使用 LLDB 的额外的格式化方式，尽管将不能使用 GDB 格式化语法。

LLDB 的格式化方法可以像以下方式使用：

	(lldb) expression -f Y -- 1430672467
	(int) $0 = 53 54 46 55             STFU

这解释了之前的 FourCC 代码。

LLDB 有以下格式化程序([http://lldb.llvm.org/ varformats.html](http://lldb.llvm.org/ varformats.html)):

* B: boolean
* b: binary
* y: bytes
* Y: bytes with ASCII
* c: character
* C: printable character
* F: complex float
* s: c-string
* i: decimal
* E: enumeration
* x: hex
* f: float
* o: octal
* O: OSType
* U: unicode16
* u: unsigned decimal
* p: pointer

### Where to go from here?

思考一下可以用表达式命令做什么。尝试通过执行 `help expression` 自己探索一些其他的表达式选项，并查看是否可以弄清楚它们在做什么。
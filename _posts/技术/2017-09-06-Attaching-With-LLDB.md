---
layout: post
title: Chapter 3 - Attaching with LLDB
category: 技术
tags: LLDB
keywords:
description:
---

> > 以下内容整理自：[<ADVANCED APPLE DEBUGGING & REVERSE ENGINEERING>](https://store.raywenderlich.com/products/advanced-apple-debugging-and-reverse-engineering) 如有出入望指正

> 本节就是讲各种 attach 应用的方法



放上介绍的更详细的原文参考：

> 	The phrase of LLDB “attaching” is actually a bit misleading. A program named debugserver (found in Xcode.app/Contents/SharedFrameworks/LLDB.framework/ Resources/) is responsible for attaching to a target process.
>
> 	If it’s a remote process, such as an iOS, watchOS or tvOS application running on a remote device, a remote debugserver gets launched on that remote device. It’s LLDB’s job to launch, connect, and coordinate with the debugserver to handle all the interactions in debugging an application.


### Attaching to an existing process

> 已经运行的进程可以使用以下方法：

	lldb -n 有道词典

通过名字直接捕获，这里会有个问题，如果你遇到可以双开的程序会给出提示:

	mylove@klonedeMacBook-Pro~]]]$lldb -n QQ
	(lldb) process attach --name "QQ"
	error: attach failed: more than one process named QQ:
	PID    PARENT USER       TRIPLE                   ARGUMENTS
	====== ====== ========== ======================== ============================
	647    1      mylove     x86_64-apple-macosx      /Applications/QQ.app/Contents/MacOS/QQ -psn_0_192559
	303    1      mylove     x86_64-apple-macosx      /Applications/QQ.app/Contents/MacOS/QQ -psn_0_49164
	
	(lldb)

这时候你可以通过 PID 捕获

	lldb -p 647

获取程序 PID 可以在终端输入

	$pgrep -x QQ
	303
	647

还有其他捕获应用的方法

	there are other ways to do the same thing. You can attach to Xcode by providing the process identifier, or PID, of a running program.

### Attaching to a future process

如果程序没有运行，使用以上方法的话就是失败，下面介绍一种方法：LLDB 可以等待应用启动时捕获

使用 -w 参数，它会等待应用启动捕获

	-w, which causes LLDB to wait until a process launches with a PID or executable name matching the criteria supplied to the -w argument.

	lldb -n Finder -w

将会在 Finder 下次启动的时候 attach，不信的话你可以新开一个终端会话窗口将 Finder 先杀掉

	pkill Finder

Finder 被杀掉后会重新启动，这时你会看到和之前一样的效果

另外一种方法就是直接 attach 可执行文件

	lldb -f /System/Library/CoreServices/Finder.app/Contents/MacOS/Finder

准备好 debug 后，输入

	(lldb) process launch

	Note: An interesting side effect is that stderr output (i.e. NSLog & company) are automatically sent to the Terminal window when manually launching a process. Other LLDB attaching configurations don’t do this automatically.

使用这种方式 attach，在终端自动输出 log，但是其他方式就不会自动输出

### Options while launching

___process launch___ 中有很多参数值得深究，如果你想更详细的列表，使用 ___help process launch___

	(lldb) help process launch
	 Launch the executable in the debugger.
	
	Syntax: process launch <cmd-options> [<run-args>]
	
	Command Options Usage:
	  process launch [-s][-A ] [-p <plugin>][-w ] [-a <arch>][-v ] [-c[<filename>]][-i ] [-o <filename>][-e ] [<run-args>]
	  ...

新开一个终端，输入

	lldb -f /bin/ls

把 ___ls___ 当做可执行的目标文件
会得到这样的输出

	(lldb) target create "/bin/ls"
	Current executable set to '/bin/ls' (x86_64).

无参数运行

	process launch

输出

	(lldb) process launch
	Process 40149 launched: '/bin/ls' (x86_64)
	输出省略
	Process 40149 exited with status = 0 (0x00000000)

___ls___ 将会在你进入的主目录启动，输出当前目录。可以使用 ___-w___ 更改当前工作目录

	(lldb) process launch -w /Applications

这个命令和下面的命令是等价的	

	$ cd /Applications
	$ ls

使用以下命令

	(lldb) process launch -- /Applications

和使用

	$ls /Applications

效果是一样的

如果现在直接把工作目录直接切换到桌面会是怎样的

	(lldb) process launch -- ~/Desktop
	Process 40286 launched: '/bin/ls' (x86_64)
	ls: ~/Desktop: No such file or directory
	Process 40286 exited with status = 1 (0x00000001)

这时候并不能有任何作用，这时候需要说明 ___~___ 的作用

	(lldb) process launch -X true -- ~/Desktop

这时会发现输出主目录的文件， ___-X___ 可以解释一些类似 ___~___ 的 ___shell___ 参数，在 ___LLDB___ 中有更简单的方法，使用 ___run___ 命令就可以了，想要自定义更多快捷命令请参考 ___Chapter 8, “Persisting and Customizing Commands”___

在 ___lldb___ 中输入

	(lldb) help run
	     Launch the executable in the debugger.
	
	Syntax: run [<run-args>]
	
	Command Options Usage:
	  run [<run-args>]
	
	'run' is an abbreviation for 'process launch -X true --'

这时候使用 ___run___ 来一遍

	(lldb) run ~/Desktop


怎样把输出放到另一个地方呢？在第一节的时候使用 ___-e___ 将 ___stderr___ 输出到其他终端窗口

输入以下命令：

	(lldb) process launch -o /tmp/ls_output.txt -- /Applications

___-o___ 输出到文件

输出为：

	Process 40367 launched: '/bin/ls' (x86_64)
	Process 40367 exited with status = 0 (0x00000000)

这时 ___ls___ 命令的输出是没有显示出来的

打开一个新的终端

	cat /tmp/ls_output.txt

会看到所有关于 /Applications 的所有输出

对于 ___stdin___ 的 ___-i___ 参数也是一样，输入：

	(lldb) target delete

移除 target ___ls___。然后，

	(lldb) target create /usr/bin/wc
	Current executable set to '/usr/bin/wc' (x86_64).

设置一个新的 target， ___wc____ 可以统计输入的字符个数，文本行数
添加一些用于输入的文本，新开一个窗口

	echo "hello world" > /tmp/wc_input.txt

将 hello world 写入 /tmp/wc_input.txt，作为 ___wc___ 输入

在 ___LLDB___ 中输入

	(lldb) process launch -i /tmp/wc_input.txt
	Process 40412 launched: '/usr/bin/wc' (x86_64)
	       1       2      12
	Process 40412 exited with status = 0 (0x00000000)

可以看到， 1 行， 2 单词， 12 字节
效果和以下方式相同：

	$ wc < /tmp/wc_input.txt

有时候你并不想要 ___stdin___(standard input)，这在一些类似 ___Xcode ___的 ___GUI___，但并不适用于类似 ___wc___ 和 ___ls___ 的终端命令

举个栗子，运行无参数的 ___wc___

	(lldb) run
	Process 40460 launched: '/usr/bin/wc' (x86_64)

输入 ___hello world___， 按 ___Enter___ 键，然后 ___Control + D___ 结束输入，这时你会看到标准的输出，像是这样

	(lldb) run
	Process 40460 launched: '/usr/bin/wc' (x86_64)
	hello world
	       1       2      12
	Process 40460 exited with status = 0 (0x00000000)

这时，输入：

	process launch -n

但 __wc__ 回直接退出

	Process 40596 launched: '/usr/bin/wc' (x86_64)
	Process 40596 exited with status = 0 (0x00000000)

使用 __-n__ 参数不会有 __stdin__ ；因此， __wc__ 没有数据处理就直接退出了。

通过以上介绍，就可以使用 __LLDB__ attach __GUI__ 和 __non-GUI__ 程序了。

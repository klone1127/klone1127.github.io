---
layout: post
title: 实现页面跳转
category: 技术
tags: iOS
keywords:
description:
---

>实现页面间的跳转

今天做了几个页面，本想通过按钮事件从一个控制器的页面直接跳到另一个控制器的页面，但怎么也不成功，哎，说到底还是学的慢，方法不知道。。。最终还是在UIViewController中找到了，不容易啊。

***为button添加点击事件***
​	    
	     [signButton addTarget:self action:@selector(UIButtonAtion) forControlEvents:(UIControlEventTouchUpInside)];

***跳转方法的实现***
	- (void)UIButtonAtion{
			 SignUpViewController * subView = [[SignUpViewController alloc]init];
		[self presentViewController:subView animated:YES completion:nil];
	 	[subView release];
	}
	
	SignUpViewController 为要跳转到的ViewController

​	
	API方法：
	- (void)presentViewController:(UIViewController *)viewControllerToPresent
	                 animated:(BOOL)flag
	               completion:(void (^)(void))completion

 ***Parameters***                  
<table>
    <tr>
        <td>viewControllerToPresent</td>
        <td>The view controller to display over the current view controller’s content.</td>
    </tr>
    <tr>
        <td>flag</td>
        <td>Pass YES to animate the presentation; otherwise, pass NO.</td>
    </tr>	
    <tr>
        <td>completion</td>
        <td>The block to execute after the presentation finishes. This block has no return value and takes no parameters. You may specify nil for this parameter.</td>
    </tr>
</table>

---
layout: post
title: 模态时显示半屏
category: 技术
tags: iOS
keywords:
description:
---

> 平常模态时，都是全屏的,这次项目要求突然要半屏,还真有点儿不习惯...
> 下面给出主要代码:

	从一个 viewController 模态到 PickerViewController:

	PickerViewController *pic = [[PickerViewController alloc] init];
	pic.modalPresentationStyle = UIModalPresentationCustom;
	[self presentViewController:pic animated:YES completion:^{
	    pic.view.superview.backgroundColor = [UIColor clearColor];
	}];
	[pic release];

>pic.view.superview.backgroundColor = [UIColor clearColor]; 
>写过这句后,在模态到 PickerViewController 时就已经是透明的了.

然后,在 PickerViewController 中添加一个 View ，设置其 frame 为主屏幕一半

	UIView *preView = [[UIView alloc] initWithFrame:CGRectMake(
		0, 
		[UIScreen mainScreen].bounds.size.height / 2, 
		[UIScreen mainScreen].bounds.size.weight, 
		[UIScreen mainScreen].bounds.size.height / 2
		)];
	preView.backgroundColor = [UIColor colorWithRed:0.289 green:0.323 blue:0.358 alpha:1.000];
	[self.view addSubview:preView];

   效果图：

<img src="http://olnx7jkmx.bkt.clouddn.com/halfScreenView?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />


---
layout: post
title: 圆形边框的按钮
category: 技术
tags: iOS
keywords:
description:
---

>生成一个带圆形边框的按钮
	做按钮的时候想到以前做网站时做的简洁风格按钮，于是就想用现在学的iOS技术做一个，刚开始只想到用两个圆嵌套，做成那种风格的，但是，这毕竟不是按钮啊，而且代码量有点儿大，最后查看API在加上Google到的CGColorSpaceRef，得到现在的按钮。

    UIButton * signButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    
    signButton.frame = CGRectMake(80, 330, 50, 30);
     
    //signButton.backgroundColor = [UIColor cyanColor];
     
    [signButton setTitle:@"登 录" forState:(UIControlStateNormal)];
     
    [signButton setTitleColor:[UIColor blueColor] forState:(UIControlStateNormal)];
    
    生成圆形带颜色按钮的方法
    
    signButton.layer.borderWidth = 1.5;
    
    signButton.layer.cornerRadius = 4.5;
    
    signButton.layer.masksToBounds = YES;
    
    // 设置颜色空间为rgb，用于生成ColorRef
    
    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
    
    // 新建一个红色的ColorRef，用于设置边框（四个数字分别是 r, g, b, alpha）
    
    CGColorRef borderColorRef = CGColorCreate(colorSpace,(CGFloat[]){ 1, 2, 3, 1 });
    
    signButton.layer.borderColor = borderColorRef;
    
    [self.view addSubview:signButton];
    

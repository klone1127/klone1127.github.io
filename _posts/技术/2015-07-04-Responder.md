---
layout: post
title: 响应者链
category: 技术
tags: iOS
keywords:
description:
---

>响应者链(Responder)

    ResponderView *blueView = [[ResponderView alloc]initWithFrame:CGRectMake(30, 30, 260, 224)];

***关闭用户交互***
***blueView.userInteractionEnabled = NO;***
​    


    // blueView.userInteractionEnabled = NO;关闭用户交互，交互关闭，响应者链就断了，对响应者关闭，链上的后面位置响应者就无法接收事件
    blueView.tag = 102;
    
    blueView.backgroundColor = [UIColor blueColor];
    
    [greenView addSubview:blueView];
    
    ResponderView *yellowView = [[ResponderView alloc]initWithFrame:CGRectMake(30, 30, 200, 164)];
    yellowView.tag = 103;
    
    yellowView.backgroundColor = [UIColor yellowColor];
    
    [blueView addSubview:yellowView];


    [greenView release];
    [blueView release];
    [yellowView release];

responder object 是能够响应并处理事件的对象，是构成响应链和事件传
递链的节点。

举个栗子，当手指去触摸屏幕上 UIView 的实例对象 aView。触摸的动作
会产生一个触摸事件 UIEventTypeTouches，而接收触摸事件的对象 aView，就是一个 responder object。

一个事件有可能被多个 responder 接收，第一个接收事件的 responder 就叫 first responder。

在 iOS 中，几乎所有类都是 responder，比如 UIWindow、UIView、UIControl、UIControllers 等，而这些类都有一个共同的父类 —— UIResponder。UIResponder 声明了用于处理事件的接口，并定义了默认的行为。

参考文档：
[1]: [iOS Events and Responder Chain](https://www.zybuluo.com/MicroCai/note/66142)

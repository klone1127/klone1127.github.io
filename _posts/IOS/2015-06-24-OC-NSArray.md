---
layout: post
title: OC数组的操作
category: IOS
tags: NSArray,NSMutableArray
keywords: 
description: 
---
>OC数组的操作

oc数组也学了有几天了，今天在这里总结一下常用的方法


NSArray: 不可变

NSMutableArray: 可变

下面是从苹果官方API截下来的简介，从图中可以看到***NSArray***的父类是 ***NSObject***，其子类是 ***NSMutableArray***，所以 ***NSArray***的所有方法在***NSMutableArray***中都能使用。当然，***NSMutableArray***也有自己特有的方法。

![1](/public/img/IOS/about-NSArray.png)
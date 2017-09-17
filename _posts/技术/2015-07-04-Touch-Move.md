---
layout: post
title: 移动视图在屏幕上的位置
category: 技术
tags: iOS
keywords:
description:
---

>单指触摸屏幕移动视图在屏幕上的位置,方法实现后，可以在屏幕上随意移动视图

	// 触摸事件方法---移动
	- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event{
	
	 // 1.获取手指对象
	  UITouch * touch = [touches anyObject];
	
	  // 2.获取当前的手指在自身视图上的位置
	  CGPoint currentPoint = [touch locationInView:self];
	
	  // 3.获取手指移动前的位置
	  CGPoint previousPoint = [touch previousLocationInView:self];
	
	  // 4.计算移动增量
	  CGFloat dx = currentPoint.x - previousPoint.x;
	  CGFloat dy = currentPoint.y - previousPoint.y;
	
	  // 5.获取中心点之前的位置
	  CGPoint center = self.center;
	  self.center = CGPointMake(center.x + dx, center.y + dy);
	
	 }

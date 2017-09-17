---
layout: post
title: 触摸屏幕随机变换颜色
category: 技术
tags: iOS
keydwords:
description:
---

>单指触摸屏幕时，随机改变视图的颜色

随机产生颜色的类目
​	
	#import "UIColor+Random.h"
	#define COLORVALUE arc4random()%256/255.0
	
	@implementation UIColor (Random)
	随机颜色方法
	+ (UIColor *)randomColor{
	
		 return [UIColor colorWithRed:COLORVALUE green:COLORVALUE blue:COLORVALUE alpha:1.0f];
		 
	}	
	
	@end

​	
TouchView

	#import "touchView.h"
	#import "UIColor+Random.h"
	
	@implementation touchView
	
	// 如果想要一个视图做出回应，需要实现下面这些方法
	
	// 触摸到屏幕时发生
	- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event{
	//   UITouch
	
	根据RGB值得到颜色
	//    self.backgroundColor = UIColor colorWithRed:<#(CGFloat)#> green:<#(CGFloat)#> blue:<#(CGFloat)#> alpha:<#(CGFloat)#>
	
	//self.backgroundColor = [UIColor randomColor];
	
	// 找到父视图的属性    
	//super.superview.backgroundColor = [UIColor randomColor];
	
	NSLog(@"%s",__FUNCTION__);
	  
	[self changeMyselfCenter];
	
	}
	
	// 触摸事件被中断时 触发（如：触摸时来电话）
	- (void)touchesCancelled:(NSSet *)touches withEvent:	(UIEvent *)event{
	
	    NSLog(@"%s",__FUNCTION__);
	
	}
	
	// 手指离开屏幕时 触发
	- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event{
	
	//    touches 存放手指对象
	 	 UITouch * touch = [touches anyObject];	   
	  	 if (1 == touch.tapCount){
			// 当视图识别为单击操作时，延迟执行下面的方法，看是否会有双击操作发生   
		     [self performSelector:@selector(changeMyselfBackgroundColor) withObject:nil afterDelay:(0.02)];
		  }else if(2 == touch.tapCount){
		// 当识别为双击的时候，取消之前方法的操作
		[NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(changeMyselfBackgroundColor) object:nil];
		 self.superview.backgroundColor = [UIColor randomColor];
	   }else{
	     self.backgroundColor = [UIColor randomColor];
	   }
		__FUNCTION__打印方法名
		 	 NSLog(@"%s",__FUNCTION__);
	
	}
	
	// 手指在视图上移动
	-  (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event{
	
	    NSLog(@"%s",__FUNCTION__);
	
	}
	
	- (void)changeMyselfBackgroundColor{
		  self.backgroundColor = [UIColor randomColor];
	}
	
	- (void)changeMyselfCenter{
		  self.center = CGPointMake(arc4random()%101 + 100, arc4random()%200 + 120);
	}

以上就是随机颜色的代码了。

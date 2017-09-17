---
layout: post
title: UITextField小记
category: iOS
tags: UI
keywords:
description:
---

>UITextField

>> ***UITextField(文本域)*** 继承自 ***UIControl***,它是在UILabel的基础功能上多了文本编辑的功能，用户可以进行输入和修改文本 

#### 1.初始化

	CGRect rect = CGRectMake(10, 100, 300, 50) ;
	UITextField *textField = [[UITextField alloc]initWithFrame:rect];

给textField 赋 tag值，目的是为能通过父视图找到textField

	textField.tag = 100;


#### 2.设置属性

* 1、设置背景颜色

   textField.backgroundColor = [UIColor cyanColor];

* 2、设置alpha值(透明度)

   textField.alpha = 0.4;
* 3、文本框样式


    typedef NS_ENUM(NSInteger, UITextBorderStyle) {
       UITextBorderStyleNone,		无
       UITextBorderStyleLine,		线
       UITextBorderStyleBezel,	直角
       UITextBorderStyleRoundedRect 圆角
     };
    
    设置为圆角
    textField.borderStyle = UITextBorderStyleRoundedRect;

* 4、占位符,占位的字符串一旦有内容输入就消失

   textField.placeholder = @"我想说...";

* 5、设置字体颜色

    textField.textColor = [UIColor blackColor];
* 6、设置文本

   textField.text = @"你还好吗";
* 7、文本对齐设置

   textField.textAlignment = NSTextAlignmentCenter;
* 8、设置文本框是否能被编辑,设置为NO时不能编辑，默认值是YES

   textField.enabled = YES;
* 9、当开始输入文本时，是否清空文本框里的内容,默认值为NO

   textField.clearsOnBeginEditing = YES;
* 10、设置虚拟键盘上return的按键样式


     typedef NS_ENUM(NSInteger, UIReturnKeyType) {
    	  	UIReturnKeyDefault,
    	 	UIReturnKeyGo,
    	 	UIReturnKeyGoogle,
    		UIReturnKeyJoin,
    	 	UIReturnKeyNext,
    	 	UIReturnKeyRoute,
    	 	UIReturnKeySearch,
    	 	UIReturnKeySend,
    	 	UIReturnKeyYahoo,
    	 	UIReturnKeyDone,
    	 	UIReturnKeyEmergencyCall,
    };
    	
    	自定义按键样式
    	textField reloadInputViews
    	
    	Google
    	textField.returnKeyType = UIReturnKeyGoogle;
    	
    	设置弹出键盘样式
    	默认
    	textField.keyboardType = UIKeyboardTypeDefault;

* 11、设置输入文本框文字以密码形式显示,默认值为YES

   textField.secureTextEntry = YES;

* 12、自定义弹出视图

    UIView * inPutView = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 320, 250)];
        textField.inputView = inPutView;
        inPutView.backgroundColor = [UIColor cyanColor];
        inPutView.alpha = 0.4;
        [inPutView release];
        自定义键盘上方的辅助视图
    UIView * accessView = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 320, 30)];
        accessView.backgroundColor = [UIColor cyanColor];
        textField.inputAccessoryView = accessView;
        [accessView release];

* 13、设置文本框清理模式

       typedef NS_ENUM(NSInteger, 	UITextFieldViewMode) {
            从不出现
            UITextFieldViewModeNever,
            编辑时出现
            UITextFieldViewModeWhileEditing,
            除了编辑外都出现
            UITextFieldViewModeUnlessEditing,
            一直出现
            UITextFieldViewModeAlways
       };
       	  设置一直出现
       	  textField.clearButtonMode = 	UITextFieldViewModeAlways;
* 14、设置文本框的代理

    *针对某个类的代理(协议)的命名规则：类名 + delegate
      *当一个类的属性是遵循了某个协议的属性，它的命名是：delegate
      *delegate代理的属性语义特性使用assign。    
      *要遵循代理(协议)这个类必须是已知类(因为我们要在遵循代理的类的.m文件中实现协议这个方法)

    	设置代理，并实现UITextFieldDelegate内的协议
    	textField.delegate = self;
* 15、自动校验

      typedef NS_ENUM(NSInteger, 	UITextAutocorrectionType) {
           UITextAutocorrectionTypeDefault,
           UITextAutocorrectionTypeNo,
           UITextAutocorrectionTypeYes,
      };
      textField.autocorrectionType = UITextAutocorrectionTypeNo; 

#### 3.添加到父视图

	[self.window addSubview:textField];

#### 4.释放(使用MRC下使用)

	[textField release];
**********************************

>实现UITextFieldDelegate中的协议

 	收起键盘

	-  (BOOL)textFieldShouldReturn:(UITextField *)textField{
		[textField resignFirstResponder];
		return YES;
	   }
	
	   使用触摸事件收起键盘
	   - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event{
	
	      UITextField * textField = (UITextField *)[self.window viewWithTag:100];
	
	       [textField resignFirstResponder];
	
	   }



​		
​		




​    	 
​    	 
​	

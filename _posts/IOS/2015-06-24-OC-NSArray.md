---
layout: post
title: OC数组的操作
category: IOS
tags: IOS
keywords: 
description:
---
>OC数组的操作

oc数组也学了有几天了，今天在这里总结一下常用的方法


	NSArray ：不可变

	NSMutableArray：可变

下面是从苹果官方API截下来的简介，从图中可以看到 ***NSArray***的父类是 ***NSObject***，其子类是 ***NSMutableArray***，所以 ***NSArray***的所有方法在 ***NSMutableArray***中都能使用。当然，***NSMutableArray***也有自己特有的方法。

![1](/public/img/IOS/about-NSArray.png)

###创建数组

>构造器创建数组

1.创建一个空数组	
	
	+ (instancetype)array
	
	Creates and returns an empty array.
	
	NSArray *emptyArray = [NSArray array];
	
2.创建和返回一个数组，新建的数组包含另一个数组

	+ (instancetype)arrayWithArray:(NSArray *)anArray
	
	NSArray *returnArray = [NSArray arrayWithArray:anotherArray];

创建一个数组returnArray，并将数组anotherArray中的值传进来


3.从一个文件新建一个数组
	
	+ (NSArray *)arrayWithContentsOfFile:(NSString *)aPath
	
	NSString *path = @"/file.plist";
	
    NSArray *createWithFile = [NSArray arrayWithContentsOfFile:path];

我今天使用这个方法读取文件的时候，发现只有plist文件能读取，之前使用txt文件打印的结果都是null，之后在stackoverflow找到有人说只能读取plist文件。

![2](/public/img/IOS/arrayWithContentsOfFile-Can-Be-Only-Used-With-Plists-01.png)

![3](/public/img/IOS/arrayWithContentsOfFile-Can-Be-Only-Used-With-Plists-02.png)

4.创建时添加一个对象

	+ (instancetype)arrayWithObject:(id)anObject
	Creates and returns an array containing a given object.
	
	NSArray *crateWithOneObject = [NSArray arrayWithObject:@"Apple"];

5.创建时添加多个对象

	+ (instancetype)arrayWithObjects:(id)firstObj, ...

	NSArray *crateWithMoreObject = [NSArray arrayWithObject:@"Apple",@"Google",nil];
	
最后有个nil，其实它在这里的作用和C语言字符串中'\0'的作用一样，都是结束的意思。
下面提供一个苹果API中的一个例子，定义了三种不同类型的对象，最后存入NSArray中。

![3](/public/img/IOS/arrayWithObjects-diff-objects.png)

>init方法创建

1.创建和返回一个数组，新建的数组包含另一个数组
	
	- (instancetype)initWithArray:(NSArray *)anArray
	
	NSArray *arrayInitWithArray = [[NSArray alloc]initWithArray:otherArray];
返回值:***An array initialized to contain the objects in anArray. The returned object might be different than the original receiver.***

2.创建时添加多个对象

	- (instancetype)initWithObjects:(id)firstObj, ...
	
	NSArray *arrayinitWithArray = [[NSArray alloc]initWithObjects:@"Apple",@"Google"，@"MicroSoft",nil];
	
**********************

判断是否包含某一个对象，判断的是指针对象值，不是对象中的值

	- (BOOL)containsObject:(id)anObject
	
	BOOL isContains = [arrayinitWithArray containsObject:@"Apple"]
	
返回值:***YES if anObject is present in the array, otherwise NO.***

>>Discussion:

This method determines whether anObject is present in the array by sending an ***isEqual***: message to each of the array’s objects (and passing anObject as the parameter to each ***isEqual***: message).

计算数组对象中的个数

	@property(readonly) NSUInteger count

	NSUInteger count = [arrayinitWithArray count];
	
	

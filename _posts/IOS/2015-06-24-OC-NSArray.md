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
##NSArray
###创建数组

>构造器创建数组

####1.创建一个空数组	
	
	+ (instancetype)array
	
	Creates and returns an empty array.
	
	NSArray *emptyArray = [NSArray array];
	
####2.创建和返回一个数组，新建的数组包含另一个数组

	+ (instancetype)arrayWithArray:(NSArray *)anArray
	
	NSArray *returnArray = [NSArray arrayWithArray:anotherArray];

创建一个数组returnArray，并将数组anotherArray中的值传进来


####3.从一个文件新建一个数组
	
	+ (NSArray *)arrayWithContentsOfFile:(NSString *)aPath
	
	NSString *path = @"/file.plist";
	
    NSArray *createWithFile = [NSArray arrayWithContentsOfFile:path];

我今天使用这个方法读取文件的时候，发现只有plist文件能读取，之前使用txt文件打印的结果都是null，之后在stackoverflow找到有人说只能读取plist文件。

![2](/public/img/IOS/arrayWithContentsOfFile-Can-Be-Only-Used-With-Plists-01.png)

![3](/public/img/IOS/arrayWithContentsOfFile-Can-Be-Only-Used-With-Plists-02.png)

####4.创建时添加一个对象

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

####1.创建和返回一个数组，新建的数组包含另一个数组
	
	- (instancetype)initWithArray:(NSArray *)anArray
	
	NSArray *arrayInitWithArray = [[NSArray alloc]initWithArray:otherArray];
返回值:***An array initialized to contain the objects in anArray. The returned object might be different than the original receiver.***

####2.创建时添加多个对象

	- (instancetype)initWithObjects:(id)firstObj, ...
	
	NSArray *arrayinitWithArray = [[NSArray alloc]initWithObjects:@"Apple",@"Google"，@"MicroSoft",nil];
	
**********************

####判断是否包含某一个对象，判断的是指针对象值，不是对象中的值

	- (BOOL)containsObject:(id)anObject
	
	BOOL isContains = [arrayinitWithArray containsObject:@"Apple"]
	
返回值:***YES if anObject is present in the array, otherwise NO.***

>Discussion:

This method determines whether anObject is present in the array by sending an ***isEqual***: message to each of the array’s objects (and passing anObject as the parameter to each ***isEqual***: message).

####计算数组对象中的个数

	@property(readonly) NSUInteger count

	NSUInteger count = [arrayinitWithArray count];
	
####获取指定下标的对象

	- (id)objectAtIndex:(NSUInteger)index

	NSString *str = [arrayinitWithArray objectAtIndex:0];
	
获取数组的第一个对象。

####获取下标里的所有元素，返回一个元素的集合(数组)

	- (NSArray *)objectsAtIndexes:(NSIndexSet *)indexes
	
	NSMutableIndexSet *indexs = [NSMutableIndexSet indexSet]; 
	NSArray *arrIndexes = [arrayinitWithArray objectAtIndex:indexes];

####获取第一个元素

	@property(nonatomic, readonly) id firstObject
	
	id arr1 = [arrayinitWithArray firstObject];
	
####获取最后一个元素

	@property(nonatomic, readonly) id lastObject
	
	id arr2 = [arrayinitWithArray lastObject];

####获取连续范围内的对象

	- (NSArray *)subarrayWithRange:(NSRange)range

	NSRange NSMakeRange ( NSUInteger loc, NSUInteger len );
	
	
	NSArray *arr = [createWithOneObject subarrayWithRange:NSMakeRange(0, 2)];
	
	
NSMakeRange 其实就是一个结构体
	
	NSRange NSMakeRange (
    NSUInteger loc,
    NSUInteger len
	);
	
>下面是官方API上的一个例子

![4](/public/img/IOS/subarrayWithRange-range.png)
	
	
####删除

	[arrayinitWithArray removeAllObjects];//删除所有元素 
	
	[arrayinitWithArray removeLastObject];//删除最后一个元素
	
	[arrayinitWithArray removeObject:@"Apple"];//删除指定对象，会将所有对象删除
	
	[arrayinitWithArray removeObject:@"Google" inRange:NSMakeRange(1, 2)];//删除指定范围中的指定对象
	
	[arrayinitWithArray removeObjectAtIndex:0];//删除指定下标的元素
	
	[arrayinitWithArray removeObjectsInRange:NSMakeRange(1, 2)];//删除指定范围中得元素
	
####修改
	
	[arrayinitWithArray replaceObjectAtIndex:2 withObject:@"IBM"];  	
	
用数组otherArray替换数组indexs

	[arrayinitWithArray replaceObjectsAtIndexes:indexs withObjects:otherArray];  
	
指定范围内修改

	[arrayinitWithArray replaceObjectsInRange:NSMakeRange(1, 3) withObjectsFromArray:array];
	
####交换数组中的内容 1 和 3 代表位置

	[arrayinitWithArray exchangeObjectAtIndex:1 withObjectAtIndex:3];  
	
####数组(存储的全是字符串)拼接

	- (NSString *)componentsJoinedByString:(NSString *)separator
	
	[arrayinitWithArray componentsJoinedByString:@","];  

API上的例子:

	For example, this code excerpt writes "here be dragons" to the console:
	
	NSArray *pathArray = [NSArray arrayWithObjects:@"here", @"be", @"dragons", nil];
	
	NSLog(@"%@",[pathArray componentsJoinedByString:@" "]);
	
	
####在数组的尾部增加一个对象,返回一个新的数组对象，因为他是不可变的 

	- (NSArray *)arrayByAddingObject:(id)anObject

	[arrayinitWithArray arrayByAddingObject:@"yuan"];  
	
	
####数组的遍历

	//数组的遍历
	for(int i=0;i<arrayinitWithArray.count;i++){
	    NSString *str = [arrayinitWithArray objectAtIndex:i];
	    NSLog(@"%@",str);
	}
	
	//快速遍历
	for(NSString *str in arrayinitWithArray){
	    NSLog(@"%@",str);
	}
	
____________________________

##NSMutableArray

###创建

构造器创建:

	+ (instancetype)arrayWithCapacity:(NSUInteger)numItems

	NSMutableArray *mutArray = [NSMutableArray arrayWithCapacity:10];
	数字10是为了向内存申请空间，也可以为其他数字，最好和自己使用的数量相同，可以提高效率
	
init创建:
	
	- (instancetype)initWithCapacity:(NSUInteger)numItems
	
	NSMutableArray *initMutArray = [[NSMutableArray alloc]initWithCapacity:10];
	
###添加

添加了一个字符串

	- (void)addObject:(id)anObject
	
	[initMutArray addObject:@"zhang"];
	
将mutArray添加到initMutArray，initMutArray就成了二维数组
	
	[initMutArray addObject:mutArray];

把mutArray中的所有元素全部添加到initMutArray中  

	- (void)addObjectsFromArray:(NSArray *)otherArray
	
	[initMutArray addObjectsFromArray:mutArray]; 
在指定位置添加元素
	
	- (void)insertObject:(id)anObject atIndex:(NSUInteger)index

	[initMutArray insertObject:@"abc" atIndex:0];
 
###替换

	- (void)replaceObjectAtIndex:(NSUInteger)index  withObject:(id)anObject

	[initMutArray replaceObjectAtIndex:0 withObject:@"zhang"]; 
	
###排序

摘自API：

	- (void)sortUsingSelector:(SEL)comparator
			
	Sorts the array’s elements in ascending order, as determined by the comparison method specified by a given selector.
	 
	 
>comparator	
 
A selector that specifies the comparison method to use to compare elements in the array.

The comparator message is sent to each object in the array and has as its single argument another object in the array. The comparator method should return NSOrderedAscending if the array is smaller than the argument, NSOrderedDescending if the array is larger than the argument, and NSOrderedSame if they are equal.
	
	[initMutArray sortUsingSelector:@Selector(compare :)];
	按升序排列
	
	
其实，我整理的只是NSArray和NSMutableArray方法的一部分，其他的就以后遇到了在补充过来。	

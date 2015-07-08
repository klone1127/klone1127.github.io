---
layout: post
title: OC基础之类和对象
category: iOS
tags: iOS
keywords: 
description: 
---

从今天开始我的iOS开发算是起步了，第一天学习Objective-C，感觉不是很爽。作为一名Java程序猿的我，第一次使用OC语法什么的都不习惯，尤其是@到处都是，不过，今天看看还是有点儿收获的。

今天主要看了OC的对象和类，对象就不提了，Java就是一门面向对象的语言，以前看《Thinking in Java》时就知道，everything is object ，所以这部分就过了。

在Xcode中类的定义和实现是分别保存在两个文件中的，一个是.h文件，一个是.m文件。  
  
.h文件中写的是类的声明部分，包括类的特征和行为的声明(方法声明)。   

.m文件中写的是行为的实现(方法的实现)。

类的实现不能放在.h文件中的。

*OC中类的定义包含两部分：*
1.接口部分：对外声明类的特征和行为。（也就是对类进行一下说明，类似于说明书）
2.实现部分：对内实现行为。（内部的具体实现）
*接口的部分标志是：*

    @interface   类名 : NSObject{
    数据类型  实例变量;
    ...
    }

    - (返回值)对象名;   “-”是对象的方法
    + (返回值)方法名    “+”是类的方法 
    @end

如：

    @interface Person : NSObject{
    //类的特征一定要写在大括号{}内
    //在OC中字符串用NSString *
    //@public在这里的意思和Java中是差不多一样的，加了之后此处的实例变量能够被访问，此外还有@private和@protected

        @public
        NSString *_name; 
    
        NSInteger _age; 

        NSString *_sex;

    }

    - (void)sayHi:(NSString *)talkWithYou;

    @end

NSObject 是一个基类，几乎所有的类都是继承这个类（需要注意的是在OC中的基类或者根(root)类并不是只有这一个，例如NSProxy也是OC的基类）。

*实现部分的标志是：*

    @implementation  类名

    - (返回值)对象名{
   
    }

    @end

如：

    @implementation Person

    - (void)sayHi:(NSString *)talkWithYou{
        NSLog(@"%@",talkWithYou);
    }

    @end

在OC中使用NSLog(); 函数进行输出



在声明和实现写好之后，就要在main.m文件中new个对象了，在main函数中使用以下方法新建一个：

    ​Person *person = [[Person alloc]init];

另外还有一个

    Person *Person = [Person new];

这个方法也可以新建一个，[Person new]基本等同于[[Person alloc]init]，alloc 在分配内存的时候使用了zone。
不过，new方法并不受推荐，如果使用new的话，初始化方法就被固定死了，只能调用init，alloc则可以用自己的或定制的方法进行初始化。

例如：我们可以定制Person类的初始化

    @interface Person : NSObject{
        @public
        NSString *_name; 
    }

    - (id)init;
    //id返回值类型，表示初始化完成的对象。
    @end

    @implementation Person
    - (int)init{

        _name = "鹏伟";
       return self;
    }
    @end

这样，在Person的_name就初始化为"鹏伟"了。

上面代码里有个self，self是个指针，指向方法的调用者，谁调用就指向谁，代表当前所指对象，可以利用self->成员变量名   访问当前对象内部的实例变量，有点像Java中的this。。。

嗯，就先到这里吧。







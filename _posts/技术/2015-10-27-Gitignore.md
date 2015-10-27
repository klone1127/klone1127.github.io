---
layout: post
title: .gitignore 文件的配置和使用
category: 技术
tags: Git
keywords: 
description:
---

>以前对于 ***.gitignore*** 文件只是简单的了解，就是知道它是干嘛用的，很少自己动手配置，这次就写下来吧，省的以后用到了再忘了，还得在去其他地方找。

>>在使用 __Git__ 管理项目时，有些文件是没必要(自动生成的文件、编译产生的文件等)或者不想(帐号、密码等)提交被管理的，这时我们就可以通过配置 ***Git*** 的 .gitignore 文件或者 /info/exclude 来忽略那些我们不想提交的文件。

* ***.gitignore*** 文件为全局的文件过滤设置，会影响远端和本地
* .git/info/exclude 主要用于忽略那些不想加入版本控制的文件而又不会影响远端，只对本地文件忽略配置。

### 创建 .gitignore 文件的方法
	
	打开终端(Windows 这个方法也多，就不啰嗦了)，进入被 git 管理的项目目录下
	创建 .gitignore 文件
	touch .gitignore 
### 文件配置规则

*  空行被直接忽略
*  \#: 注释
*   /: 后边的都会被忽略
*   !: 表示 ***非*** 的意思，***例如: !example.md ，这个文件将被添加到版本控制***
* \**: 通配符，***\*\*/foo 就是指 foo 文件夹；example/\*\*/foo 指的也是 foo 文件夹***
*  \*: 通配符，***例如: example/*.md  指的是 example 文件夹下的所有以 md 为后缀名的文件***
*  文件名: 忽略指定文件，***example.md*** 忽略指定的 example.md

### EXAMPLES

	# examples
	# 此处为注释
	
	# / 后的所有文件都会被忽略
	/*
	
	# example.md 不会被忽略，继续被管理
	!example.md
	
	# a 文件夹下(中间或许还有其他文件夹)的 b 文件被忽略
	a/**/b.md 
	
	# a 文件夹下的所有文件被忽略
	a/**
	
	# example 文件夹下的所有以 markdown 为后缀的文件都会被忽略
	example/*.markdown

就大概写这几个吧。

### 注意事项

>如果一个文件a在建立 .gitignore 文件或配置 .git/info/exclude 前就已经被管理了，这是需要清除缓存
	
	git rm --cached
	
### 扩展阅读

* [***GitHub Help*** Using Git / Ignoring files](https://help.github.com/articles/ignoring-files/)

* [***git***--fast-version-control  gitignore](http://git-scm.com/docs/gitignore)

* [***廖雪峰Git教程***——忽略特殊文件](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013758404317281e54b6f5375640abbb11e67be4cd49e0000)
* [***Github 上的模板***](https://github.com/github/gitignore)

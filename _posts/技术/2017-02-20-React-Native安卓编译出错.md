---
layout: post
title: react-native 安卓编译出错
category: 技术
tags: react-native
keywords:
description:
---
### react-native 安卓编译出错

新建了一个 RN 项目，使用 Android Studio 导入打开项目的时候报错:

> Error:(24, 0) Could not find method android() for arguments [build_bvzjv4mjldob73mp1agwx72x5$_run_closure2@7a053e6a] on root project 'TestHelloWorld' of type org.gradle.api.Project.
> <a href="openFile:/Users/mylove/TestHelloWorld/android/build.gradle">Open File</a>
>
> ![Gradle_Error](http://olnx7jkmx.bkt.clouddn.com/2017-02-20-React-Native%E5%AE%89%E5%8D%93%E7%BC%96%E8%AF%91%E5%87%BA%E9%94%99-Gradle_Error.png?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10)
>
> 

首先我是根据提示信息 Try Again , ————   无用

看 log 信息也没得出个所以然来

点了 Open File 后直接定位到了Gradle Script/build.gradle 下的 

    android {    
    	compileSdkVersion 23
    	buildToolsVersion '23.0.2'
    }
    
    dependencies {
    }

，虽然是直接定位到了这里，但并不知道解决办法，在网上搜了一番，试了多个方法后也是无效的

最后，无奈删除了这两段代码，然后编译 -> 运行，嗯，可以了，至于原因，我还是没想清楚为啥，稍后对这块了解的多了再回来解释这个问题！
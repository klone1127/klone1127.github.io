---
layout: post
title: CentOS7和win7双系统下修复win7启动项
category: 技术
tags: Linux
keywords:
description:
---

>把Centos6换成Centos7后出现了个让人蛋疼的问题，启动项找不到，，，而且Centos7和centos6启动文件有很大区别，当时我就差挨个的看文件源码了，google很久好不容易找到了个问题差不多的，改写之后运行了下，问题解决。

把下面代码加到 /boot/grub2/grub.cfg 文件中，重启就可以看到win7的启动项了

    ### BEGIN /etc/grub.d/30_os-prober ###
    menuentry 'Windows 7 (loader) (on /dev/sda1)' --class windows --class os         $menuentry_id_option 'osprober-chain-C2AE5AA3AE5A8FB1' {
    insmod part_msdos
    insmod ntfs
    set root='hd0,msdos1'
    chainloader +1
    }
    ### END /etc/grub.d/30_os-prober ###
    
####C2AE5AA3AE5A8FB1 这串为盘号（添自己对应的盘）

hd0,msdos1   是win7启动项所在盘
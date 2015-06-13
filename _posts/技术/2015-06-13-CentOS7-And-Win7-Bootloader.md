---
layout: post
title: CentOS7和win7双系统下修复win7启动项
category: 技术
tags: Linux
keywords:
description:
---

>把下面代码加到 /boot/grub2/grub.cfg 文件中，重启就可以看到win7的启动项了
####'### BEGIN /etc/grub.d/30_os-prober ###'

    menuentry 'Windows 7 (loader) (on /dev/sda1)' --class windows --class os         $menuentry_id_option 'osprober-chain-C2AE5AA3AE5A8FB1' {
    insmod part_msdos
    insmod ntfs
    set root='hd0,msdos1'
    chainloader +1
    }
    ### END /etc/grub.d/30_os-prober ###
    
###C2AE5AA3AE5A8FB1 这串为盘号###（添自己对应的盘）

hd0,msdos1   win7启动项所在盘
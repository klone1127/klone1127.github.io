---
layout: post
title: 自定义 Xcode Warning
category: 技术
tags: iOS
keywords:
description:
---
> 在项目中总免不了想要添加一些类似 TODO/FIXME 之类的提示信息，但 Xcode 自带的 warning 提示有时用着并不是那么爽， 还不识别中文(不要问我为啥不用英文写… :joy:)

>#### 下面介绍一种通过 shell 脚本添加自定义 warning 的方式，其他方式还没怎么去探索

* 点击 target -> Build Phases -> 添加 New Run Script Phases

    ````shell
    TAGS="TODO:|FIXME:"
    echo "searching ${SRCROOT} for ${TAGS}"
    find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -print0 | xargs -0 egrep --with-filename --line-number --only-matching "($TAGS).*\$" | perl -p -e "s/($TAGS)/ warning: \$1/"
    ````

> 在项目中添加  // TODO: 编译后就可以看到了。

<img src="http://olnx7jkmx.bkt.clouddn.com/Custom-Xcode-Warning.png?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />


> 参考:

> > [Jeffrey Sambells-Generate Xcode Warnings from TODO Comments](http://jeffreysambells.com/2013/01/31/generate-xcode-warnings-from-todo-comments)


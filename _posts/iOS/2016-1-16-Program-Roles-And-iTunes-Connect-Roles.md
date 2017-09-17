---
layout: post
title: 开发者角色和iTunes Connect角色
category: iOS
tags: iOS
keywords:
description:
---

> 苹果开发者帐号的几种类型大家都很清楚了，但团队开发中添加的成员的角色以及其所拥有的权限应该有一些人没去注意过。
> iOS中的证书、PP(Provisioning Profiles)文件、添加测试机等一些操作并不是所有开发组中每个成员都可以操作的，这需要一定的权限.iTunes Connect 也不是所有成员能够操作的，，，下面是我从官网找到的对于各个角色的解释，写(截)下来算是一种记录吧。

Team 开发中有 Team Agent、Admin、Member 这三个角色。

Team Agent 就是帐号的注册者，一个团队只能有一个。
Admin 可有 Team Agent 指定，可以有多个
Member 这个角色可以指定多个，权限很受限

### 各个角色对应的权限如下：

<img src="http://olnx7jkmx.bkt.clouddn.com/TeamMemberRoles?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />

> 在 iTunes Connect 中根据职责的不同权限也是不同的，存在的角色有:Legal、Admin、Finance、Technical、Sales、Marketing。

> Legal 是帐号的注册者，和团队开发的 Team Agent 是同一个人

> Admin 由Legal指定的管理员

> Finance 财务？看名字再看苹果给出的解释(Assign this role to those who need access to financial reports and banking information)应该就是了

> Teachnical 技术支持，官方解释(Assign this role to those who need to manage your apps)

> Sales 销售，官方解释(Assign this role to those who need access to reporting, marketing, and ad campaign information)

> Marketing 市场，官方解释(Assign this role to the person who manages your marketing materials and promotional artwork. Users with the Marketing role are contacted by the App Store team if your app is chosen to be featured)

### 各个角色的权限如下:

<img src="http://olnx7jkmx.bkt.clouddn.com/iTunes-Connect-Roles?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />


### 企业开发中各个角色的权限

>企业应用不用上传至 Appstore
<img src="http://olnx7jkmx.bkt.clouddn.com/Apple-Developer-Enterprise-Program-Roles?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />


#### iOS Developer University Program (大学生一类)
<img src="http://olnx7jkmx.bkt.clouddn.com/iOS-Developer-University-Program-Roles?imageView2/0/interlace/1/q/100|watermark/2/text/a2xvbmUuc3BhY2U=/font/5b6u6L2v6ZuF6buR/fontsize/500/fill/I0YzRjBGMA==/dissolve/86/gravity/SouthEast/dx/10/dy/10" width="300px" />

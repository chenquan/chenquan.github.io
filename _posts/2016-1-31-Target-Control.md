---
layout: post
title: 如何在Xcode中添加多个targets进行版本控制 
categories: [blog]
tags: [iOS]
description: 如何在项目中添加多个targets进行版本控制
---

### 多个targets的作用
  
   在iOS开发中,我们经常会遇到对同一个app开发多个版本,这样就涉及到xcode里面通过添加多个targets来进行版本.
  
  
### 如何添加多个targets
    
   这里先做个说明,对于添加多个targets有多个方式,现在分享下再网上看到的两种创建多个target方式:
   
   * 通过File-->New-->Target，然后选择其中一个模板来创建.
   
   * 另一种方法是通过对原有的target做duplicate。
   
   本文里面添加方式是自己根据查找资料,自己完善总结的,以下就是添加多个targets的步骤.下面将以图文说明. 	


####以下是自己方法实现添加多个Target的步骤:
 
1.首先,先看下添加完成后的效果图,如下:

![](/img/targets_post/target_test_1.png
)
---
layout: post
title: iOS性能优化分析和优化
categories: [blog]
tags: [App,Optimization]
header-img: "img/post-bg-kuaidi.jpg"
description:  About app performance optimization
---  



对于性能优化这块，可以分为三个等级：初级、中级、高级。没个等级对应如下。

##### 初级
 - 用ARC管理内存。
 - 有正确的地方使用`reuseIdentifier`。
 - 避免使用庞大的`xib`。
 - 不要使用`block`主线程。
 - 使用`Image Views`来调整图片大小。
 - 正确的使用`Collection`。
 
##### 中级
  - 重用和延迟加载`views`。
  - `cache`问题。
  - 渲染界面问题。
  - 处理内存警告问题。
  - 避免反复处理数据。
  - 选择正确的数据格式。
  - 正确的设定`background image`。
  - 减少使用`web`特性。
  - 优化`tableview`。
  - 设置正确的数据存储选项。
  - 设定`Shadow Path`。
  
##### 高级
 - 加速启动时间。
 - 使用`Autorelease Pool`。
 - 有选择性的缓存图片。
 - 尽量避免日期转换。
 
 
 下面对以上一些谈一下自己看法。
 
###### 避免使用庞大的`xib`(xib缺点)。
  1. 对人开发管理相对不方便。
  2. `Xib`载入相对于纯代码会慢。
  3. 过多的`xib`会导致项目的包过大。
  4. 尝试为每个Controller配置一个单独的XIB，尽可能把一个View Controller的view层次结构分散到单独的XIB中去。
 
##### 避免阻塞线程
      
       dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        // switch to a background thread and perform your expensive operation
        
        dispatch_async(dispatch_get_main_queue(), ^{
            // switch back to the main thread to update your UI
            
        });
    });
    
##### 处理内存警告方法如下：
   
  - 在app delegate中使用applicationDidReceiveMemoryWarning: 的方法。
  - 在你的自定义UIViewController的子类(subclass)中覆盖didReceiveMemoryWarning。
  - 注册并接收 UIApplicationDidReceiveMemoryWarningNotification 的通知。

 



  
 
 

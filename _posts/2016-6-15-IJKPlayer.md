---
layout: post
title: 集成并使用IJKPlayer视频直播框架
categories: [blog]
tags: [Swift,IJKPlayer]
description: 集成并使用IJKPlayer视频直播框架
---

##### 关于IJKPlayer
`IJKPlayer`是基于FFmpeg n3.0的视频直播的框架。可支持`iOS`,`Android`，方便使用。

##### 如何在项目中集成IJKPlayer

关于如何集成，详细细节可以看官方教程，链接如下：
[官方教程](https://github.com/Bilibili/ijkplayer)
如何觉得`github`上讲的不详细，实现起来相对较难，可以看如下链接：
[iOS中集成ijkplayer视频直播框架](http://www.jianshu.com/p/1f06b27b3ac0) 个人觉得相对比较详细，已亲测，并成功集成到项目中。

ps:集成此框架最主要的就是如何把ijkplayer打包成.framework框架。 主要有两种方式：

 第一种方式：将官方提供的Demo工程导入到你的项目工程，操作如下
 		  
    1.open ios/IJKMediaDemo/IJKMediaDemo.xcodeproj with Xcode
    2.Import into Your own Application
    3.Select your project in Xcode.File -> Add Files to ... -> Select ios/IJKMediaPlayer/IJKMediaPlayer.xcodeproj
    4.Select your Application's target.Build Phases -> Target Dependencies -> Select IJKMediaFramework 
    
 第二种方式： ijkplayer 打包成framework导入工程中使用，这打包成framework过程此[文章](http://www.jianshu.com/p/1f06b27b3ac0)里面有详细说明，其中合并包操作需要注意路径是否填写正确，不然就会报错：
  	
  	
  	 lipo -create "真机版本路径" "模拟器版本路径" -output "合并后的文件路径"
  	
  	 操作如下：（路径替换成自己项目里面包的路径）
     lipo -create /Users/admin/Library/Developer/Xcode/DerivedData/IJKMediaPlayer-cakhkkabtvnmptbgwbuzbnuhbgoa/Build/Products/Release-iphoneos/IJKMediaFramework.framework/IJKMediaFramework /Users/admin/Library/Developer/Xcode/DerivedData/IJKMediaPlayer-cakhkkabtvnmptbgwbuzbnuhbgoa/Build/Products/Release-iphonesimulator/IJKMediaFramework.framework/IJKMediaFramework -output /Users/admin/Library/Developer/Xcode/DerivedData/IJKMediaPlayer-cakhkkabtvnmptbgwbuzbnuhbgoa/Build/Products/IJKMediaFramework


##### 如何使用IJKPlayer



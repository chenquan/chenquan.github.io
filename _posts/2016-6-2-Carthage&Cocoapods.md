---
layout: post
title: Carthage&Cocoapods
categories: [blog]
tags: [Carthage&Cocoapods]
description: Carthage&Cocoapods
---

#### 概要
  iOS开发时，项目中会引用许多第三方库，所以运用统一管理第三方的工具必不可少。目前来说，比较好用的第三方管理工具有[Carthage](https://github.com/Carthage/Carthage) & [CocoaPods](https://github.com/CocoaPods/CocoaPods),下面罗列一下这两个工具的使用步骤。
  
#### Carthage&CocoaPods安装
  对于这两个管理工具的安装，这里将不细致说明，具体的安装方式可看官方说明，具体可以看以下是连接：
  
  1.[Carthage安装教程](https://github.com/Carthage/Carthage)
  
  2.[CocoaPods安装教程](http://code4app.com/article/cocoapods-install-usage)
  
#### Carthage&CocoaPods使用

##### 1.CocoaPods使用步骤

```
1. 打开终端cd到项目目录下
	
2. 建立Podfile（配置文件）命令：touch Podfile
	
3. 执行命令: open Podfile 进行编辑。 或者 执行 vim Podfile操作，在终端打开Podfile文件进行编辑。

4. 在Podfile文件中输入需要管理的第三方。例如Masonry.那就在Podflie中加入如下代码并保存：
   	- platform :ios, ‘8.0’ 
	- pod 'Masonry'

5. cd到项目目录下，执行命令：pod install（安装） & pod update(更新) 命令。
	
6. 等待安装，安装完成后，打开项目，以xcworkspace为后缀的文件来打开项目。

```
ps:对于cocoaPods使用简单的命令就是这些，具体细节或者更多的命令，可以参考具体的官方文档。

##### 2.Carthage使用步骤

```
1. 打开终端并cd到对应的项目目录下。
- $ cd ~/Path/Project ,后面的路径替换成你的项目所在的路径即可

2. 创建一个空的carthage文件：命令：touch Cartfile

3. 打开Cartfile文件。有以下两种方式：
 - 命令1： open Cartfile
 - 命令2： open -a Xcode Cartfile
 
4. 加入需要管理的第三方。例如SnapKit. 输入一下代码并保存：

 - github "SnapKit/SnapKit" >= 0.15.0
 - ps: 这里版本的含义如下，以3.0为例：
  ① ~> 3.0 表示使用版本3.0以上但是低于4.0的最新版本，如3.5, 3.9。
  ② == 3.0 表示使用3.0版本。
  ③ >= 3.0表示使用3.0或更高的版本。
  ④ 如果你没有指明版本号，则会自动使用最新的版本。
 
5. 执行终端命令: carthage update --platform ios 下载和编译所需的第三方库。

6. 安装完成后可以执行一下命令查看： open Carthage。

7. 接下来需要将第三方framework文件拖拽到xCode执行目录下步骤如下：
 - 点击PROJECT，选择Target,然后选择General,将需要的framework文件拖到Linked frameworks and Binaries内，并将状态status从Required选择到Optional。

8. import对应第三方头文件即可。

```
 

  
  
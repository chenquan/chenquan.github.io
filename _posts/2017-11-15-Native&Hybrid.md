---
layout: post
title: App开发的几种模式
categories: [blog]
tags: [Native、Hybrid、React Native、Web App]
header-img: "img/post-bg-alitrip.jpg"

description: App开发的几种模式 
---  

![post_bg_react_app](/img/post_bg_react_app.png)

![post_bg_weex](/img/post_bg_weex.jpeg)

#### 当前app开发有一下五大类型：

- **Native App**
- **Web App**
- **Hybrid App**
- **React Native App**
- **Weex App**

##### Native App 

​     传统的原生`App`开发模式，`Android`基于`Java`语言，底层调用`Google`的 `API`；`iOS`基于`OC`或者`Swift`语言，底层调用`App`官方提供的`API`。

###### 优缺点

- 性能好，交互性强。
- 用户体验好。
- 开发成本高，无法跨平台。
- 维护成本高。



##### Web App

​      移动端的网站开发，将页面部署到服务器上，用户使用浏览器访问。

###### 优缺点

 - 开发成本低,可以跨平台,调试方便 。
 - 更新最为快速。
 - 无需安装`App,不会占用手机内存。
 - 用户体验差。
 - 必须连接网络。
 - 功能受限制。
 - 用户留存率低。

##### Hybrid App 

​       混合开发，也就是半原生半`Web`的开发模式，有跨平台效果。

###### 优缺点 

-  开发成本较低，可以跨平台，调试方便。
- 维护成本低，可复用。
- 更新较为自由。
- 部分性能要求的页面可用原生实现。
- 相比原生,性能仍然有较大损耗。
- 不适用于交互性较强的`app`。

##### React Native App

​    `Facebook`发起的开源的一套新的APP开发方案，`Facebook`在当初深入研究`Hybrid`开发后，觉得这种模式有先天的缺陷，所以果断放弃，转而自行研究,后来推出了自己的`ReactNative`方案，不同于`H5`，也不同于原生，更像是用`JS`写出原生应用。

###### 优缺点

- 开发成本大于`Hybrid`模式,但是小于原生模式,大部分代码可复用。
- 性能体验高于`Hybrid`,不逊色与原生。
- `React-native`的思想是多个平台可以写多套代码，但其使用的是同一套语言框架。
- `React-native`承认了各个平台之间的差异，退而求其次，在语言和框架层面对平台进行抽象，从方法论的角度去解决多平台开发的问题。
- 这种模式是统一由`JS`编写，有着独特的语法，所以只需要学习一次，即可同时开发`Android`和`iOS`。
- 虽然可以部分跨平台，但并不是`Hybrid`中的一次编写，两次运行那种，而是不同平台代码有所区别。
- 开发人员学习有一定成本。
- `React Native`基于`React.js`。



##### Weex App

`Weex`是阿里巴巴公司与2016年6月开源的一种用于构建移动跨平台的UI框。

###### 优缺点

- 轻量级，语法简单，易于使用。
- 可扩展,丰富内置组件,可扩展的API。
- `weex`基于`vue.js`。
- `weex`的思想是多个平台，只写一套代码。
- `weex`的目标在于抹平各个平台的差异性，从而简化应用开发。

![post_weex_react](/img/post_weex_react.jpg)

![post_weex-vs-react](/img/post_weex-vs-react.jpeg)

#### 参考资源如下

- [百度脑图](http://naotu.baidu.com/file/1eb556f3380e8189be859348527ec518?token=a5a049eb4c618e70)
- [Hybrid App](https://www.cnblogs.com/dailc/archive/2016/10/04/5930238.html)  
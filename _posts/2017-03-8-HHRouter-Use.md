---
layout: post
title: HHRouter使用
categories: [blog]
tags: [HHRouter]
header-img: "img/post-bg-rwd.jpg"

description:  HHRouter使用
---

#### HHRouter 

  GitHub地址：[https://github.com/lightory/HHRouter](https://github.com/lightory/HHRouter) <br/>
  Function &：Yet another URL Router for iOS. Clean, Fast & Flexible. 
   
#### 优势

   - 减少`ViewController`之间的耦合。 <br/>
   
     1.使得控制器之间的跳转，不需要依赖于后者。  
     2.对用后者控制器的属性变化，我们只需要修改后者控制器即可，后者需要的各项属性可以通过`URL Router`的参数完成传递。
 
   - 使用`URL Router`每个ViewController都拥有唯一且不重复的URL，对界面跳转、追踪用户浏览记录、开放`URL Scheme`都很有帮助。

#### 使用

###### 1.安装方式`Cocoapods`
 
	pod 'HHRouter', '~> 0.1.8'
 	
 	导入头文件：
 	#import <HHRouter/HHRouter.h>    
    
###### 2.使用方式
  
  - 使用方式1: AController -> BController 不传参数
  
         //UserAViewController.m 点击按钮跳转代码：push到BViewController
         UIViewController *userBController = [[HHRouter shared] matchController:kHHRouterUserBViewControllerName];
         [self.navigationController pushViewController:userBController
                                         animated:YES];
                                         
         //APPDelegate.m文件代码如下
         [[HHRouter shared] map:kHHRouterUserBViewControllerName
         toControllerClass:NSClassFromString(kHHRouterUserBViewControllerName)];                            
  


 -  使用方式2：AController -> BController 穿参数userId,name
  
         //UserAViewController.m 点击按钮跳转代码：push到CViewController，userId值为1
         UIViewController *userCController = [[HHRouter shared] matchController:[NSString stringWithFormat:@"/%@/1/zhangsan",kHHRouterUserCViewControllerName]];
   	    [self.navigationController pushViewController:userCController
                                             animated:YES];
        
         //APPDelegate.m文件代码如下        
		 [[HHRouter shared] map:[NSString stringWithFormat:@"/%@/:userId/:name/",kHHRouterUserCViewControllerName]
          toControllerClass:NSClassFromString(kHHRouterUserCViewControllerName)];
         
         //UserCViewController.m 打印参数值 1 zhangsan
          NSLog(@"userId::::::::::%@",self.params[@"userId"]);
          NSLog(@"userId::::::::::%@",self.params[@"name"]);
         
 - 使用方式3：URL 查询参数
  		   	  
          [self.navigationController pushViewController:userCController animated:YES];
          NSLog(@"::::::%@",userCController.params[@"tabIndex"]);
          UIViewController *userCController = [[HHRouter shared] matchController:[NSString stringWithFormat:@"/%@/1/zhangsan/?tabIndex=3", kHHRouterUserCViewControllerNae]]; 
 
 - 使用方式4： Block 调用
 	
        [[HHRouter shared] map:@"/user/add/"
                  toBlock:^id(NSDictionary* params) {
					return @"test";
        }];

        HHRouterBlock block = [[HHRouter shared] matchBlock:@"/user/add/?a=1&b=2"];
	    NSLog(@"block::::%@",block(@"text" : @"test"));
        [[HHRouter shared] callBlock:@"/user/add/?a=1&b=2"];
       
 	
#### ps：Demo下载地址[Demo](https://github.com/zfx5130/HHRouterTestDemo)


---
layout: post
title: Runtime
categories: [blog]
tags: [runtime]
description: runtime
---

##### Runtime简介

- `Runtime`即运行时，是一套底层`C`语言的`API`。是`iOS`内部的核心之一，我们平日编写的`OC`代码，底层都是基于它来实现的。例如如下代码：

  ```
  [receiver message];
  // 底层运行时会被编译器转化为：
  objc_msgSend(receiver, selector)
  // 如果其还有参数比如：
  [receiver message:(id)arg...];
  // 底层运行时会被编译器转化为：
  objc_msgSend(receiver, selector, arg1, arg2, ...)
  ```

ps: `OC`是运行时机制，其中最只要的是消息机制。在编辑时并不能决定真正调用那个函数，只是在真正运行的时候才会根据函数的名称找到对应的函数来调用。在编译阶段，`OC`可以调用任何函数，及时函数并未实现，只要声明过就不会报错。

##### Runtime作用

- 发送消息
- 交换方法
- 动态添加方法
- 分类添加属性
- 字典转模型

##### 给Category添加属性

```
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.

    // 给系统NSObject类动态添加属性name

    NSObject *objc = [[NSObject alloc] init];
    objc.name = @"噢mygade";
    NSLog(@"%@",objc.name);

}
@end

// 定义关联的key
static const char *key = "name";

@implementation NSObject (Property)

- (NSString *)name
{
    // 根据关联的key，获取关联的值。
    return objc_getAssociatedObject(self, key);
}

- (void)setName:(NSString *)name
{
    // 第一个参数：给哪个对象添加关联
    // 第二个参数：关联的key，通过这个key获取
    // 第三个参数：关联的value
    // 第四个参数:关联的策略
    objc_setAssociatedObject(self, key, name, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}
@end
```



##### RunTime调用拦截 

```
+ (BOOL)resolveClassMethod:(SEL)sel;
+ (BOOL)resolveInstanceMethod:(SEL)sel;
//后两个方法需要转发到其他的类处理
- (id)forwardingTargetForSelector:(SEL)aSelector;
- (void)forwardInvocation:(NSInvocation *)anInvocation;
```

- 第一个方法是当你调用一个不存在的类方法的时候，会调用这个方法，默认返回`NO`，你可以加上自己的处理然后返回`YES`。
- 第二个方法和第一个方法相似，只不过处理的是实例方法。
- 第三个方法是将你调用的不存在的方法重定向到一个其他声明了这个方法的类，只需要你返回一个有这个方法的`target`。
- 第四个方法是将你调用的不存在的方法打包成`NSInvocation`传给你。做完你自己的处理后，调用`invokeWithTarget:`方法让某个target触发这个方法。

##### 方法交换

```
#import <objc/runtime.h>

@implementation UIViewController (swizzling)

//load方法会在类第一次加载的时候被调用
//调用的时间比较靠前，适合在这个方法里做方法交换
+ (void)load{
    //方法交换应该被保证，在程序中只会执行一次
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{

        //获得viewController的生命周期方法的selector
        SEL systemSel = @selector(viewWillAppear:);
        //自己实现的将要被交换的方法的selector
        SEL swizzSel = @selector(swiz_viewWillAppear:);
        //两个方法的Method
        Method systemMethod = class_getInstanceMethod([self class], systemSel);
        Method swizzMethod = class_getInstanceMethod([self class], swizzSel);

        //首先动态添加方法，实现是被交换的方法，返回值表示添加成功还是失败
        BOOL isAdd = class_addMethod(self, systemSel, method_getImplementation(swizzMethod), method_getTypeEncoding(swizzMethod));
        if (isAdd) {
            //如果成功，说明类中不存在这个方法的实现
            //将被交换方法的实现替换到这个并不存在的实现
            class_replaceMethod(self, swizzSel, method_getImplementation(systemMethod), method_getTypeEncoding(systemMethod));
        }else{
            //否则，交换两个方法的实现
            method_exchangeImplementations(systemMethod, swizzMethod);
        }

    });
}

- (void)swiz_viewWillAppear:(BOOL)animated{
    //这时候调用自己，看起来像是死循环
    //但是其实自己的实现已经被替换了
    [self swiz_viewWillAppear:animated];
    NSLog(@"swizzle");
}

@end
```

##### Runtime关联对象使用

```
objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy);

 id object                     
 表示关联者，是一个对象，变量名理所当然也是object

 const void *key             
 获取被关联者的索引key

 id value                   
 被关联者，这里是一个block
  
 objc_AssociationPolicy policy
 关联时采用的协议，有assign，retain，copy等协议，一般使用    OBJC_ASSOCIATION_RETAIN_NONATOMIC
```

###### 例子一

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
......
UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"提示"
                                                message:@"这里是xx楼"
                                               delegate:self
                                      cancelButtonTitle:@"好的"
                                      otherButtonTitles:nil];
//然后这里设定关联，此处把indexPath关联到alert上
objc_setAssociatedObject(alert, &kUITableViewIndexKey, indexPath, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
[alert show];
}

- (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
{
    if (buttonIndex == 0) {
    NSIndexPath *indexPath = objc_getAssociatedObject(alertView, &kUITableViewIndexKey);
    NSLog(@"%@", indexPath);
    }
} 
```

###### 例子二

```
    + (UIButton *)createBtnWithFrame:(CGRect)frame title:(NSString *)title actionBlock:(ActionBlock)actionBlock{  
        UIButton *button = [[UIButton alloc]init];  
        button.frame = frame;  
        [button setTitle:title forState:UIControlStateNormal];  
        [button addTarget:button action:@selector(buttonClick:) forControlEvents:UIControlEventTouchUpInside];  
          
        objc_setAssociatedObject (button , &keyOfMethod, actionBlock, OBJC_ASSOCIATION_COPY_NONATOMIC);  
          
        return button;  
    }  
```

```
- (void)buttonClick:(UIButton *)button{  
      
    //通过key获取被关联对象  
    //objc_getAssociatedObject(id object, const void *key)  
    ActionBlock block1 = (ActionBlock)objc_getAssociatedObject(button, &keyOfMethod);  
    if(block1){  
        block1(button);  
    }  
      
    ActionBlock block2 = (ActionBlock)objc_getAssociatedObject(button, &keyOfBlock);  
    if(block2){  
        block2(button);  
    }  
}  
  
- (void)setActionBlock:(ActionBlock)actionBlock{  
    objc_setAssociatedObject (self, &keyOfBlock, actionBlock, OBJC_ASSOCIATION_COPY_NONATOMIC );  
}  
  
- (ActionBlock)actionBlock{  
    return objc_getAssociatedObject (self ,&keyOfBlock);  
} 
```

```
- (void)uiConfig{  
    //第一个按钮  
    UIButton *button1 = [UIButton createBtnWithFrame:CGRectMake((ScreenWidth - 100)/2, (ScreenHeight - 50)/2 - 50, 100, 50) title:@"按钮" actionBlock:^(UIButton *button) {  
        float r = random()%255/255.0;  
        float g = random()%255/255.0;  
        float b = random()%255/255.0;  
        self.view.backgroundColor = RGB(r, g, b);  
    }];  
    button1.backgroundColor = [UIColor lightGrayColor];  
    [self.view addSubview:button1];  
      
    //第二个按钮  
    UIButton *button2 = [UIButton createBtnWithFrame:CGRectMake((ScreenWidth - 100)/2, CGRectGetMaxY(button1.frame) + 50, 100, 50) title:@"按钮2" actionBlock:nil];  
    button2.actionBlock = ^(UIButton *button){  
        NSLog(@"---%@---",button.currentTitle);  
    };  
    button2.backgroundColor = [UIColor lightGrayColor];  
```





​                          



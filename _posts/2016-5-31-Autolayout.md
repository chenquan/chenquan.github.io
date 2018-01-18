---
layout: post
title: autolayout布局及约束冲突问题
categories: [blog]
tags: [约束冲突]
description: autolayout布局及约束冲突问题
---

#### 使用Autolayout布局1 -- ConstraintWithItem

   对于autolayout布局，我想大家都已经很熟悉了。到现在为止，越来越多的第三方约束框架，例如Masonry等，更方面的使得纯代码布局更加的方便。但是在某些情况下使用第三方并不是非常方面，那么就不得不用系统原生的方式来添加约束。那么接下来我主要是针对系统原生autolayout布局约束方式来加以说明，目前我了解到的添加约束方式有一下三种:
   
###### NSLayoutConstraint方式添加约束

    NSLayoutConstraint *centerXConstraint = [NSLayoutConstraint 
        constraintWithItem: _button                   //firstItem
                 attribute: NSLayoutAttributeCenterX  //firstAttribute
                 relatedBy: NSLayoutRelationEqual     //relation   
                    toItem: _button.superview         //secondItem
                 attribute: NSLayoutAttributeCenterX  //secondAttribute
                multiplier: 1.0                       //multiplier
                  constant: 0]
                  
	centerXConstraint.active = YES;
                  
 - 我们可以通过上面这段代码清楚的看到布局的相关属性和代码的对应，如果你在xcode中通过查找进入到了NSLayoutConstraint的类文件中，你还会发现这些属性中只有`constant`是可写的，这意味着你没办法通过正常方式设置`multipier`这样的值来改变某个控件在父视图中的宽度。尽管KVC可以做到这一点，但这不应该是解决方式。因此，我们需要通过创建新的约束并且移除旧的约束来实现登录按钮的动画效果。在`iOS8`之前这个工作无疑是繁杂的，我们需要通过：
 			
		- (void)addConstraint:(NSLayoutConstraint *)constraint;
		- (void)addConstraints:(NSArray<__kindof NSLayoutConstraint *> *)constraints;
		- (void)removeConstraint:(NSLayoutConstraint *)constraint;
		- (void)removeConstraints:(NSArray<__kindof NSLayoutConstraint *> *)constraints;
                      
                      
- 这一系列方法来增删约束，但在`iOS8`之后，NSLayoutConstraint提供了`active`的BOOL类型的变量供我们提供设置约束是否有效，这个值设置`NO`的时候约束就失效。同样我们创建了一个约束对象之后只需要设置`active`为`YES`之后就会自动生效了。因此，根据上面的公式，我们修改代码如下：

        _loginLeftConstraint.active = NO;
		_loginRightConstraint.active = NO;
	    //创建宽高比约束
    	NSLayoutConstraint *ratioConstraint = [NSLayoutConstraint constraintWithItem: 		_signInButton attribute: NSLayoutAttributeWidth relatedBy: NSLayoutRelationEqual 		toItem: _signInButton attribute: NSLayoutAttributeHeight multiplier: 1. constant: 0];
    	ratioConstraint.active = YES;
    	_loginRatioConstraint = ratioConstraint;
                       
- 增删约束实现动画时，你还要记住的是当一个约束的`active`属性被设为`NO`之后，即便我们重新将其激活，这个约束依旧是无效的，必须重新创建。

ps：这种添加约束的方式我想大家都已经很熟悉，这里不多说，具体了解可参考以下：

   - [iOS开发动画篇之layout动画深入](http://allluckly.cn/%E6%8A%95%E7%A8%BF/tuogao34?from=timeline&isappinstalled=1)
   
   - [Autolayout约束动画化](http://www.cocoachina.com/ios/20160331/15841.html)

#### 使用AutoLayout约束布局2 -- VFL(Visual format language)
```
+ (NSArray *)constraintsWithVisualFormat:(NSString *)format options:(NSLayoutFormatOptions)opts metrics:(NSDictionary *)metrics views:(NSDictionary *)views; 
- (void)addConstraints:(NSArray *)constraints; //UIView

```			

###### 参数介绍:

- `format`:此参数为你的VFL语句，比如:`@"H:|-[button]-|"`

- `opts`:枚举参数，默认写0，具体跟据你所实现的需求去选择你想要的枚举

- `metrics`:这里是一个字典，当在format中使用了动态数据比如上现这句:`@"H:|-[button(==width)]-|"`,表示这个button的宽度为width,那么这个参数去哪里找呢？就是在这个字典里面找到key对就的值，如果没有找到这个值，app就会crash.

- `views`:顾名思义，这是传所有你在vfl中使用到的view，那在上面这句例子中的应该怎么传呢？结果是这样的：NSDictionaryOfVariableBindings(button).如果你使用到了多个view，就可以这样NSDictionaryOfVariableBindings(button,button1,button3...),这个名字也要跟参数format中的一一对应，缺一不可.	

###### 使用规则
```
|: 表示父视图
-:表示距离
V:  :表示垂直
H:  :表示水平
>= :表示视图间距、宽度和高度必须大于或等于某个值
<= :表示视图间距、宽度和高度必须小宇或等于某个值
== :表示视图间距、宽度或者高度必须等于某个值
ps: >=、<=、== 限制最大为1000

例如:
1.|-[view]-|:  视图处在父视图的左右边缘内
2.|-[view]  :   视图处在父视图的左边缘
3.|[view]   :   视图和父视图左边对齐
4.-[view]-  :  设置视图的宽度高度
5.|-30.0-[view]-30.0-|:  表示离父视图 左右间距  30
6.[view(200.0)] : 表示视图宽度为 200.0
7.|-[view(view1)]-[view1]-| :表示视图宽度一样，并且在父视图左右边缘内
8. V:|-[view(50.0)] : 视图高度为  50
9: V:|-(==padding)-[imageView]->=0-[button]-(==padding)-| : 表示离父视图的距离为Padding,这两个视图间距必须大于或等于0并且距离底部父视图为padding。
10:  [wideView(>=60@700)]  :视图的宽度为至少为60 不能超过  700 ，最大为1000
```

###### 简单使用

```
@interface ViewController ()
  
@end
  
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    UIButton *button=[[UIButton alloc]init];
    [button setTitle:@"点击一下" forState:UIControlStateNormal];
    button.translatesAutoresizingMaskIntoConstraints=NO;
    [button setBackgroundColor:[UIColor blackColor]];
    [self.view addSubview:button];
    NSArray *constraints1 = 
    [NSLayoutConstraint constraintsWithVisualFormat:@"H:|-[button]-|" options:0 metrics:nil views:NSDictionaryOfVariableBindings(button)];  
    NSArray *constraints2= 
    [NSLayoutConstraint constraintsWithVisualFormat:@"V:|-20-[button(==30)]" options:0 metrics:nil views:NSDictionaryOfVariableBindings(button)];
    [self.view addConstraints:constraints1];
    [self.view addConstraints:constraints2];
}
  
@end

```

#### Autolayout约束布局方式3 -- NSLayoutAnchor(iOS9.0)

  iOS9中另一个新增的API是NSLayoutAnchor。它的出现不仅仅是让使用代码添加约束变得简洁明了。通过该类强大的静态检查能力，还提供了额外的约束正确定保证。例子如下：
  
```
 
    UILayoutGuide *view_Guide = self.view.layoutMarginsGuide;
    
    /**
       左边黄色view
     */
    UIView *yellow_View = [[UIView alloc]init];
    yellow_View.backgroundColor = [UIColor yellowColor];
    yellow_View.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addSubview:yellow_View];
    //左边距约束
    NSLayoutConstraint *yellowView_Leading = [yellow_View.leadingAnchor constraintEqualToAnchor:view_Guide.leadingAnchor constant:10];
    //顶部约束
    NSLayoutConstraint *yellowView_Top = [yellow_View.topAnchor constraintEqualToAnchor:view_Guide.topAnchor constant:84];
    //宽度约束
    NSLayoutConstraint *yellowView_Width = [yellow_View.widthAnchor constraintEqualToConstant:100];
    //高度约束
    NSLayoutConstraint *yellow_Height = [yellow_View.heightAnchor constraintEqualToConstant:100];
    [NSLayoutConstraint activateConstraints:@[yellowView_Leading,yellowView_Top,yellow_Height,yellowView_Width]];
    
    yellowViewTopConstraint = yellowView_Top;
    
     /**
        居中的红色view
     */
    UIView *middleView = [[UIView alloc]init];
    middleView.backgroundColor = [UIColor redColor];
    middleView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addSubview:middleView];
    //水平居中
    NSLayoutConstraint *middleView_CenterX = [middleView.centerXAnchor constraintEqualToAnchor:view_Guide.centerXAnchor];
    //垂直居中
    NSLayoutConstraint *middleView_CenterY = [middleView.centerYAnchor constraintEqualToAnchor:view_Guide.centerYAnchor];
     //宽度约束
    NSLayoutConstraint *middleView_Width = [middleView.widthAnchor constraintEqualToConstant:100];
    //高度约束
    NSLayoutConstraint *middleView_Height = [middleView.heightAnchor constraintEqualToConstant:100];
    [NSLayoutConstraint activateConstraints:@[middleView_CenterX,middleView_CenterY,middleView_Height,middleView_Width]];
    
    
```

  NSLayoutAnchor主要优势在于你不用直接创建 NSLayoutConstraint了，而是先选择你要添加约束的对象包括（UIView、NSView、UILayoutGuide），然后用这些对象的`anchor`属性通过上面的方法来构建你的约束。
  
#### 约束冲突问题 











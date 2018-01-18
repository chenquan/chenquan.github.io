---
layout: post
title: Swift闭包使用
categories: [blog]
tags: [Swift,Closures]
header-img: "img/post-bg-alitrip.jpg"

description: Swift闭包使用
---

#### 闭包概念(Closures)
- 闭包是自包含的函数代码块,可以在代码中被传递和使用。Swift 中的闭包与 C 和 Objective-C 中的代码块(blocks)以及其他一些编程语言中的匿名函数比较相似。
- 闭包可以捕获和存储其所在上下文中任意常量和变量的引用。这就是所谓的闭合并包裹着这些常量和变量,俗称`闭包`。Swift会为您管理在捕获过程中涉及到的所有内存操作。
- 全局和嵌套函数实际上也是特殊的闭包,闭包采取如下三种形式之一:
		1 全局函数是一个有名字但不会捕获任何值的闭包。		2 嵌套函数是一个有名字并可以捕获其封闭函数域内值的闭包。		3 闭包表达式是一个利用轻量级语法所写的可以捕获其上下文中变量或常量值的匿名闭包。
- Swift的闭包表达式拥有简洁的风格,并鼓励在常见场景中进行语法优化,主要优化如下:

		1.利用上下文推断参数和返回值类型。
		2.隐式返回单表达式闭包,即单表达式闭包可以省略 return 关键字
		3.参数名称简写。
		4.尾随(Trailing)闭包语法。
		#### 闭包表达式(ClosureExpressions)
- 嵌套函数是一种在较复杂函数中方便进行命名和定义自包含代码模块的方式。 当然，有时候撰写小巧的没有完整定义和命名的类函数结构也是很有用处的，尤其是在处理一些函数并需要将另外一些函数作为该函数的参数时。

- 闭包表达式是一种利用简洁语法构建内联闭包的方式。 闭包表达式提供了一些语法优化，使得撰写闭包变得简单明了。 下面闭包表达式的例子通过使用几次迭代展示了 sort 函数定义和语法优化的方式。 每一次迭代都用更简洁的方式描述了相同的功能。

#### 闭包练习(Practice)

##### 闭包表达式语法
```
    { (parameters) -> returnType in
        statements
    } 
```
闭包表达式语法可以使用常量、变量和 inout 类型作为参数，但不提供默认值。 也可以在参数列表的最后使用可变参数。元组也可以作为参数和返回值。

###### Sort函数
```
//该例子对一个 String 类型的数组进行排序，因此排序闭包需为 (String, String) -> Bool 类型的函数。
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
func backwards(s1: String, s2: String) -> Bool {
    return s1 > s2
}
var reversed = sort(names, backwards)//传入闭包
```

###### 进一步优化① - 将整个函数传入
```
    reversed = sort(names, { (s1: String, s2: String) -> Bool in
        return s1 > s2
    }) 
//修改后：
reversed = sort(names, { (s1: String, s2: String) -> Bool in return s1 > s2 } ) 

```
###### 进一步优化② - 省略参数名

```
reversed = sort(names, { s1, s2 in return s1 > s2 } ) 

```
因为排序闭包是作为函数的参数进行传入的，Swift可以推断其参数和返回值的类型。 sort 期望第二个参数是类型为 (String, String) -> Bool 的函数，因此实际上 String, String 和 Bool 类型并不需要作为闭包表达式定义中的一部分。 因为所有的类型都可以被正确推断，返回箭头 (->) 和围绕在参数周围的括号也可以被省略。

###### 进一步优化③ - 省略 return
```
reversed = sort(names, { s1, s2 in s1 > s2 } ) 

```
单行表达式闭包可以通过隐藏 return 关键字来隐式返回单行表达式的结果

###### 进一步优化④ - 参数名简写

```
reversed = sort(names, { $0 > $1 } ) 

```
Swift 自动为内联函数提供了参数名称简写功能，您可以直接通过 $0,$1,$2等名字来引用的闭包的参数的值。
如果您在闭包表达式中使用参数名称简写，您可以在闭包参数列表中省略对其的定义，并且对应参数名称简写的类型会通过函数类型进行推断。 in关键字也同样可以被省略。

###### 进一步优化⑤ - 运算符函数
```
reversed = sort(names, >) 

```
  Swift的 String 类型定义了关于大于号 (>) 的字符串实现，让其作为一个函数接受两个 String 类型的参数并返回 Bool 类型的值。 而这正好与 sort 函数的第二个参数需要的函数类型相符合。 因此，您可以简单地传递一个大于号，Swift可以自动推断出您想使用大于号的字符串函数实现。
  
###### 进一步优化⑥ - Trailing闭包
```
reversed = sort(names) { $0 > $1 } 

```  

#### Trailing 闭包
- 如果您需要将一个很长的闭包表达式作为最后一个参数传递给函数，可以使用 trailing 闭包来增强函数的可读性。
- Trailing 闭包是一个书写在函数括号之外(之后)的闭包表达式，函数支持将其作为最后一个参数调用。

```
    func someFunctionThatTakesAClosure(closure: () -> ()) {
        // 函数体部分
    }
    // 以下是不使用 trailing 闭包进行函数调用
    someFunctionThatTakesAClosure({
        // 闭包主体部分
    })
    // 以下是使用 trailing 闭包进行函数调用
    someFunctionThatTakesAClosure() {
        // 闭包主体部分
    } 
    
```		
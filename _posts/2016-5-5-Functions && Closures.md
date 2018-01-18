---
layout: post
title: Functions & Closures
categories: [blog]
tags: [Swift，函数式，闭包]
description: Swift Functions(函数式) & Closures（闭包）
---

### 函数定义

   使用 `func` 来声明一个函数，使用名字和参数来调用函数。使用 `->` 来指定函数返回值的类型,在Xcode输入func,按下回车后，系统生成如下代码:
 
```
func <#name#>(<#parameters#>) -> <#return type#> {
       <#function body#>
}
    
```
 *  `name`: 函数名
 *  `parameters` : 参数（可参数，可多类型，一般格式 参数名 : 参数类型，多参数用，分割）
 *  `return type` : 函数返回值类型（Swift中返回值类型可以返回多个值，也可以无返回值Void）
 *  `function body` : 函数体
 
### 代码示例

    func test() {
        print("无参数无返回值")
    }
    
    func test(name: String) -> Void {
        print("有参数无返回值\(name)")
    }
    
    func test(name: String, age: Int) -> String {
        //有参数有返回值（多个参数，返回String类型）
        return "姓名：\(name) age：\(age)"
    }
    
    func test1(numbers: Int...) -> Int {
        //有参数有返回值（参数为带有可变个数的参数，这些参数在函数内表现为数组的形式）
        var sum = 0
        for number in numbers {
            sum += number
        }
        return sum
    }
    
    func test(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
        //有参数有返回值（参数是一个Int类型数组，返回多个值）
        var min = scores[0]
        var max = scores[0]
        var sum = 0
        
        for score in scores {
            if score > max {
                max = score
            } else if score < min {
                min = score
            }
             sum += score
        }
        return (min, max, sum)
    }

	//打印测试数据
	test()
	test("zhangsan")
	print(test("zhangsan", age: 20))
	let scores = test([10, 6, 100, 40, 60])
	print(scores.max)
	print(scores.min)
	print(test1(434,424,546))
	print(test1())


 
### 函数嵌套
 
  函数可以嵌套。被嵌套的函数可以访问外侧函数的变量（函数嵌套基本用来重构一个太长或者太复杂的函数）,示例如下：
  
  	func function() -> Int {
       var y = 10
        func add() {
          y += 5
        }
        add()
        return y
    }
 
 
###函数做为另一个函数的返回值使用
  函数是第一等类型，这意味着函数可以做为另一个函数的返回值。
  
      
    func makeIncrementer(forIncrement amount: Int) -> () -> Int {
        var runningTotal = 0
        func incrementer() -> Int {
            runningTotal += amount
            return runningTotal
        }
        return incrementer
    }
    //打印测试数据
    let incrementByTen = makeIncrementer(forIncrement: 10)
        print(incrementByTen)


### 函数做为参数使用
	func hasAnyMatches(list: [Int], condition: Int -> Bool) -> Bool {
        for item in list {
            if condition(item) {
                return true
            }
        }
        return false
    }
    
    func lessThanTen(number: Int) -> Bool {
        return number < 10
    }
    //打印测试数据
    let numbers = [20, 14, 43, 54]
	let value = hasAnyMatches(numbers, condition: lessThanTen)
	print(value)
	
### 函数闭包
 
 函数实际上是一种特殊的闭包：它是一段能之后被调取的代码。你可以使用`{}`来创建一个匿名闭包。使用`in` 将参数和返回值类型声明与闭包函数体进行分离。
 
### 函数闭包示例
 
 	 numbers.map { (number: Int) -> Int in
            let result = 3 * number
            return result
	}
ps：有很多种创建更简洁的闭包的方法。如果一个闭包的类型已知，比如做为一个回调函数，你可以忽略参数和返回值。单个语句闭包会把它语句的值当做结果返回,示例如下：
   
  	 let numbersValue = numbers.map({ number in 3 * number })
     print(numbersValue)
 
ps： 你可以通过参数位置而不是参数名字来引用参数--这个方法在非常短的闭包中非常有用。当一个闭包做为最后一个参数传入一个函数的时候，它可以直接跟在括号的后面。当一个闭包是传给函数的唯一参数，可以完全忽略括号。示例如下：

	  let sortedNumbers = numbers.sort{$0 > $1 }
      print(sortedNumbers)
  
 

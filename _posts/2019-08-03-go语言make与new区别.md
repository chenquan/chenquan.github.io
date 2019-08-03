---
layout: post
title: go语言make与new区别
categories: [go]
tags: 
- go
description: go语言make与new区别

---
#  go语言make与new区别
> Go语言中的内建函数new和make是两个用于内存分配的原语（allocation primitives）。new 和 make 都可以用来分配空间，初始化类型，但是它们确有不同。



## 一、new(T) 返回的是 T 的指针

`new`是一个用来分配内存的内建函数，但是与C++不一样的是，它并不初始化内存，只是将其置零。也就是说，new(T)会为T类型的新项目，分配被置零的存储，并且返回它的地址，一个类型为*T的值。在Go的术语中，其返回一个指向新分配的类型为T的指针，这个指针指向的内容的值为零（zero value），但并不是指针为零。

```go
p1 := new(int)//默认初始值为0
fmt.Printf("p1 --> %#v \n ", p1) //p1 --> (*int)(0xc000060080) 
fmt.Printf("p1 point to --> %#v \n ", *p1) //p1 point to --> 0 

var p2 *int
i := 0
p2 = &i
fmt.Printf("p2 --> %#v \n ", p2) //p2 --> (*int)(0xc0000600b0) 
fmt.Printf("i --> %#p \n ", p2) //i --> (*int)(0xc0000600b0)  
fmt.Printf("p2 point to --> %#v \n ", *p2) //p2 point to --> 0 

```

上面的代码是等价的，new(int) 将分配的空间初始化为 int 的零值，也就是 0，并返回 int 的指针，这和直接声明指针并初始化的效果是相同的。

## 二、make 只能用于 slice,map,channel

**make** 只能用于 `slice`，`map`，`channel `三种类型，`make(T, args) `返回的是初始化之后的 T 类型的值，这个新值并不是 T 类型的零值，也不是指针 *T，是经过初始化之后的 T 的引用。之所以有所不同，是因为这三个类型的背后引用了使用前必须初始化的数据结构。例如，slice是一个三元描述符，包含一个指向数据（在数组中）的指针，长度，以及容量，在这些项被初始化之前，slice都是nil的。对于slice，map和channel，make初始化这些内部数据结构，并准备好可用的值。

```go
var s1 []int
if s1 == nil {
    fmt.Printf("s1 is nil --> %#v \n", s1) // s1 is nil --> []int(nil) 
}

s2 := make([]int, 3) //slice
if s2 == nil {
    fmt.Printf("s2 is nil --> %#v \n", s2)
} else {
    fmt.Printf("s2 is not nill --> %#v \n", s2)// s2 is not nill --> []int{0, 0, 0}
}
```

slice 的零值是 nil，使用 make 之后 slice 是一个初始化的 slice，即 slice 的长度、容量、底层指向的 array 都被 make 完成初始化，此时 slice 内容被类型 int 的零值填充，形式是 [0 0 0]，map 和 channel 也是类似的。

```go
var m1 map[int]string
if m1 == nil {
    fmt.Printf("m1 is nil --> %#v \n ", m1) //m1 is nil --> map[int]string(nil) 
}

m2 := make(map[int]string)
if m2 == nil {
    fmt.Printf("m2 is nil --> %#v \n ", m2)
} else {
    fmt.Printf("m2 is not nill --> %#v \n ", m2) //m2 is not nill --> map[int]string{}  
}


var c1 chan string
if c1 == nil {
    fmt.Printf("c1 is nil --> %#v \n ", c1) //c1 is nil --> (chan string)(nil) 
}

c2 := make(chan string)
if c2 == nil {
    fmt.Printf("c2 is nil --> %#v \n ", c2)
} else {
    fmt.Printf("c2 is not nill --> %#v \n ", c2)//c2 is not nill --> (chan string)(0xc000044060) 
```

记住make只用于map，slice和channel，并且不返回指针。要获得一个显式的指针，使用new进行分配，或者显式地使用一个变量的地址。

### 1、make(T, args) 返回的是 T 的 引用

如果不特殊声明，go 的函数默认都是按值传参，即通过函数传递的参数是值的副本，在函数内部对值修改不影响值的本身，但是 `make(T, args)` 返回的值通过函数传递参数之后可以直接修改，即 map，slice，channel 通过函数传参之后在函数内部修改将影响函数外部的值。

```go
func modifyValue(s []int) {
	s[0] = 1
}
```

使用`modifyValue()`:

```go
s2 := make([]int, 3)
fmt.Printf("old: %#v \n", s2) //old: []int{0, 0, 0} 

modifyValue(s2)

fmt.Printf("new: %#v \n", s2) //new: []int{1, 0, 0} 
```

与之相反：

```go
func modifyValue(s [5]int) {//
	s[0] = 1
}
```

> 注意`modifySlice(s [5]int)`的参数，可以看到参数类型为`[5]int`。这是因为我们将传入的变量是`s2`，而它的数值类型为`[5]int`

```go
var s2 [5]int//声明数组
fmt.Printf("old: %#v \n", s2) //old: [5]int{0, 0, 0, 0, 0} 
modifyValue(s2)
fmt.Printf("new: %#v \n", s2) //new: [5]int{0, 0, 0, 0, 0} 
```

这样的数据类型传参时，就是按值传参的
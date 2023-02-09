---
title: Go语言初探
tags: 
    - Go
    - 后端
categories: 
    - Go
index_img: /illustration/go-basic/index.png
excerpt: Go语言是一款静态强类型、编译型、并发型、具有垃圾回收功能的开源编程语言，近几年在服务端开发中的热度逐渐升高。
---

## Go语言背景介绍

近几年来，Go语言在服务端开发中的地位逐渐提高，越来越多的企业开始在开发中使用Go语言。Go语言（Golang）是由Google开发的一款静态强类型、编译型、并发型、具有垃圾回收功能的开源编程语言。

不同于JavaScript、Python等动态类型语言中变量类型可变的特点，Go语言的类型系统是静态强类型，和Java、C等语言一样，Go语言中的变量类型是固定的。此外，Go语言是一款编译型的语言，不同于Java、Kotlin等语言编译后的JVM字节码二进制文件需要运行在JVM中，Go语言编译得到的目标文件则是机器码二进制文件，能够直接在操作系统中运行，而无需借助其他运行环境，这与C、C++是一致的。并发是Go语言的一个重要特点，Go语言的并发模型是以CSP为基础设计的，这使并发程序的开发难度被降低。Go的垃圾回收功能使开发者不必过多考虑内存管理问题，此外，Go语言原生提供了大量的网络通信相关的库，因而使用Go进行服务端开发更加轻松。

## Go语言语法初探

Go语言的语法并不复杂，学习成本比较低。下面这段代码的功能是输出“hello world”到stdout。

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("hello world")
}
```

第1行代码声明了代码所在的package为main，main包比较特殊。它定义了一个独立可执行程序，而不是一个库。第3-4行的import语句引入了fmt包，这个包是Go语言标准库中的一个包，顾名思义是提供格式化（format）相关的函数。随后，这段代码定义了main函数，与C、C++等众多语言一样，main包下的main函数是Go程序的入口，在Go中，函数定义的关键字是`func`。这段代码的main函数中只有一句`fmt.Println("hello world")`，这一句的含义是调用fmt包的Println方法打印出hello world。

值得一提的是，Go语言依据变量和函数命名的大小写区分访问权限，约定以大写字母开头的方法和变量是暴露在外的，任何包的代码均可以访问，类似于Java中的public；而以小写字母开头的方法和变量只在包内可以被访问，类似于Java中的default；此外，以下划线`_`开头的方法和变量仅在本文件内可以被访问，类似于Java中的private。上面的Println函数以大写字母开头，因此我们才能够在main包中调用它。

不同于常见的面向对象语言，Go语言并没有类和对象的概念，也不提供继承机制。Go并不是一门面向对象的语言（OOPL），但通过Go语言提供的struct等机制可以实现面向对象开发。

```go
var a = "initial"

var b, c int = 1, 2

var d = true

var e float64

f := float32(e)

g := a + "foo"
fmt.Println(a, b, c, d, e, f) // initial 1 2 true 0 0
fmt.Println(g)                // initialfoo

const s string = "constant"
const h = 500000000
const i = 3e20 / h
```

上面这段代码展示了Go语言中的变量和赋值操作，在Go语言中，`var`关键字表示一个变量，而`const`关键字表示一个常量，常量的值在编译期就被确定。从上面的代码不难看出，Go语言变量的类型声明被放在变量名之后，例如`var a int`定义了一个整数型变量`a`。此外，Go语言支持变量类型推断，比如上面的`var d = true`中`d`的类型就有Go语言自动推断得到。

在Go语言中，有一个特别的操作符`:=`，例如`g := a + "foo"`，它的含义是定义一个变量`g`，`g`的值为`a + "foo"`，类型有Go语言自动推断这句话等同于首先定义`var g string`，再通过`g = a + "foo"`将值赋给`g`。

未完待续...
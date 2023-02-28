---
title: Go语言学习笔记
updated: 2023/2/24 16:55
tags: 
    - Go
    - 后端
categories: 
    - Go
index_img: /illustration/go-basic/index.png
excerpt: Go语言是一款静态强类型、编译型、并发型、具有垃圾回收功能的开源编程语言，近几年在服务端开发中的热度逐渐升高。
---

# Go语言背景介绍

近几年来，Go语言在服务端开发中的地位逐渐提高，越来越多的企业开始在开发中使用Go语言。Go语言（Golang）是由Google开发的一款静态强类型、编译型、并发型、具有垃圾回收功能的开源编程语言。

不同于JavaScript、Python等动态类型语言中变量类型可变的特点，Go语言的类型系统是静态强类型，和Java、C等语言一样，Go语言中的变量类型是固定的。此外，Go语言是一款编译型的语言，不同于Java、Kotlin等语言编译后的JVM字节码二进制文件需要运行在JVM中，Go语言编译得到的目标文件则是机器码二进制文件，能够直接在操作系统中运行，而无需借助其他运行环境，这与C、C++是一致的。并发是Go语言的一个重要特点，Go语言的并发模型是以CSP为基础设计的，这使并发程序的开发难度被降低。Go的垃圾回收功能使开发者不必过多考虑内存管理问题，此外，Go语言原生提供了大量的网络通信相关的库，因而使用Go进行服务端开发更加轻松。

文章内容主要参考自《Go语言圣经》中文版（[books.studygolang.com/gopl-zh/](https://books.studygolang.com/gopl-zh/)）

# Go语言语法初探

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

# 基本概念

首先介绍Go语言中变量、函数、包的命名规则。在Go语言中，一般使用驼峰法命名变量和函数，使用全小写并以下划线分割单词的命名方法命名包和Go文件。特别的，Go语言规定以小写字母开头的函数和全局变量只能在本package内使用；以大写字母开头的函数和全局变量可以在任何地方使用。

下面以一段代码为例总结Go语言中的一些基本概念。

```go
package main

import "fmt"

func main() {
    var a = "initial"
    var b int = 1
    var d bool
    d = true
    e := b + 2
    const f = 100
    const g int = 100
    fmt.Println("Hello, World")
}
```

在Go语言中，每个Go文件都有一个`package`，特别的，`main package`并不是项目目录下的`main/`子目录，而是项目根目录。`main package`表示一个Go程序而非一个Go库。在`main package`中需要定义一个`main`函数作为程序入口，Go程序将会从`main`函数开始运行。

上面代码的第1行声明了这段代码位于`main package`。第3行是一个`import`语句，与Java的`import`、C的`include`语句一样，Go的`import`语句用来引入其他的库，这里的`fmt`是Go语言的标准库之一，提供字符串格式化相关的函数，例如向`stdout`输出格式化字符串等。Go语言不同于Java、C++等语言，在两行代码之间不需要加入分号作为分割。

在`main`函数中，展示了几种不同的变量和常量定义方法，Go语言是静态强类型语言，需要在定义变量时指定类型，或者在定义时给出初始值让Go语言自动推断类型。Go语言的类型跟在变量名之后，例如上面代码中的`var d bool`。不指定类型时，Go语言将自动推断类型，例如上面代码中的`var a = "initial"`。还有一种变量定义方法是使用`:=`操作符，这时不需要写明`var`指定为变量，也不需要指定类型，只需要写明变量名即可，例如`e := b + 2`，这一句等价于`var e int = b + 2`。Go语言定义常量使用`const`关键字，例如`const f = 100`，常量的值会在编译期确定下来，而不会等到运行时再计算。

# 函数与错误处理

Go语言以`func`作为函数声明的关键字，如果有返回值，则返回值类型需要写在函数名之后，例如`func foo() string`声明了一个名为`foo`的返回值为`string`类型的函数。此外，Go语言的函数支持多返回值，例如：

```go
func foo1() (string, bool, error) {
    return "foo1", true, nil
}

func foo() {
    str, result, err := foo1()
    if err != nil {
        return
    }
}

```

上面的`foo1`函数返回了3个返回值，类型分别是`string`、`bool`和`error`，`foo`函数调用了`foo1`函数。上面的例子引出了Go语言的另一个编码约定：将错误放在返回值的最后，如果没有发生错误，则将返回结果和`nil`（作为错误的占位符）；否则返回任意的值（作为结果的占位符）和具体错误。

# 循环

在Go语言中，循环语句依赖于`for`关键字，下面是几种不同的循环：

```go
i := 1
for {
    fmt.Println("loop")
    break
}

for j := 7; j < 9; j++ {
    fmt.Println(j)
}

for n := 0; n < 5; n++ {
    if n%2 == 0 {
	continue
    }
    fmt.Println(n)
}

for i <= 3 {
    fmt.Println(i)
    i = i + 1
}
```

只使用`for`而不加其他修饰，则与Java中的`while(true)`类似（上面代码中的第一个循环）；只在`for`之后加条件，则与Java中的`while(condition)`类似（上面代码中的最后一个循环）；也可以像Java、C中一样使用`for`语句（上面代码中的第二和第三个循环）。总之，Go语言中的`for`用法非常丰富。

# 条件语句

与许多语言一样，Go语言中条件语句的关键字是`if`。Go语言中典型的条件语句语法是形如`if condition`的语句，在Go语言中，`if`关键字之后的条件并不需要使用括号包裹。如果有多个条件需要处理，则可以使用`else`和`else if`关键字。下面是一个使用`if`语句的例子：

```go
if 7%2 == 0 {
    fmt.Println("7 is even")
} else {
    fmt.Println("7 is odd")
}
```

除了上面这种`if`用法，Go语言还提供了另一种用法，即在`if`关键字后跟多个语句，语句之间用`;`分隔，最后一个语句作为条件语句，例如：

```go
if num := 9; num < 0 {
    fmt.Println(num, "is negative")
} else if num < 10 {
    fmt.Println(num, "has 1 digit")
} else {
    fmt.Println(num, "has multiple digits")
}
```

上面这段代码的第一个`if`语句就包含两个语句，第一个语句`num := 9`定义了`num`变量，第二个语句`num < 0`则是一个布尔表达式，用于描述条件。需要注意的是，在`if`语句中定义的变量的作用域仅限在条件语句之内，以上面的代码为例，`num`变量并不能在这一系列条件语句块之外使用。

# switch语句

在需要多个条件时，相比于多个`else if`，开发者通常更倾向于使用`switch-case`语句。在Go语言中`switch`语句和C++/Java中的很相似，如下：

```go
a := 2
switch a {
    case 1:
        fmt.Println("one")
    case 2:
	fmt.Println("two")
    case 3:
	fmt.Println("three")
    case 4, 5:
	fmt.Println("four or five")
    default:
	fmt.Println("other")
}
```

注意，Go语言中的`switch`语句并不需要在各个`case`的处理语句中加`break`手动跳转，Go语言只会执行匹配的`case`的语句。此外，Go语言支持多个`case`匹配同一段语句，比如上面的`case 4, 5`。除了值匹配，Go语言的`case`还支持条件表达式，例如：

```go
t := time.Now()
switch {
    case t.Hour() < 12:
	fmt.Println("It's before noon")
    default:
	fmt.Println("It's after noon")
}
```

# 数组

Go语言的数组和C语言的功能和定义方法很类似，Go的数组是定长的，且不支持使用变量的值作为数组长度，数组长度必须在定义时使用常量或字面量指定，或者在定义时给出数组的元素，由编译器推断数组长度。Go语言的数组可以是一维的，也可以是多维的（数组的数组）。Go语言的数组定义方法是：

```go
var variable_name [SIZE] variable_type
```

具体的例子如下所示，其中第4行代码就展示了定义时只给出元素而不给出长度的数组定义方法：

```
var a [5]int
a[4] = 100

b := [...]int{1, 2, 3, 4, 5}

var twoD [2][3]int
for i := 0; i < 2; i++ {
    for j := 0; j < 3; j++ {
	twoD[i][j] = i + j
    }
}
```

# 切片 (Slice)

Go语言的数组（`array`）不能动态扩容，也不能使用变量指定数组的长度，这为开发带来了不方便。使用Go语言的切片（`slice`）功能可以避开这些不便。Go语言的切片和Java中的`ArrayList`、C++中的`vector`有些类似，都是一种容量可变的数组结构。

切片的类型定义与数组基本一致，为`var identifier []type`，即未指定大小的数组。要创建一个切片，可以借助于`make`函数，例如：

```
slice1 := make([]int, 0, 5)
```

`make`函数各个参数的意义分别是类型、长度和初始容量，即`make([]type, length, capacity)`，其中初始容量`capacity`为可选参数，这意味着在数据数量可以估计时可以通过事先指定`capacity`来减少切片扩容的开销。`make`函数的第一个参数类型为`[]type`，即数组类型，这个参数指定了切片的数据类型；第二个参数`length`为切片的初始长度，上面的例子中第二个参数为`0`意味着创建一个空的不含任何元素的切片。

除了使用`make`函数创建切片，还可以直接使用`[] type`的形式创建切片，例如：

```
s := [] int {1, 2, 3}
```

这条语句创建了一个包含三个元素（1，2，3）的整数类型的切片，该切片的长度和容量均为3。注意，这条语句与创建数组的语句不同，要创建数组，应当使用：

```
s := [...] int {1, 2, 3}
```

这会创建一个长度为3的数组，而非切片，无法进行容量动态增加和元素追加。

切片可以像数组一样使用下标访问其中的元素，也可以使用类似`slice[:]`的方式访问其中的多个元素。例如：

```
s := [] int {1, 2, 3}
a := s[idx1:idx2] // 取出从下标idx1到idx2的元素，创建一个新切片
b := s[idx1:] // 取出从下标idx1到最后的全部元素，创建一个新切片
d := s[:idx2] //取出从第一个元素到下标idx2的元素，创建一个新切片
```

要获得一个切片的长度和容量，可以使用`len`、`cap`这两个函数，例如`len(s)`将返回切片`s`的长度，而`cap(s)`将返回切片`s`的容量。

如果要向切片追加元素，可以使用`append`函数，例如：

```
s := [] int {1, 2, 3}
s = append(s, 4) // s现在为{1, 2, 3, 4}
s = append(s, 5, 6, 7) // s 现在为{1, 2, 3, 4, 5, 6, 7}
```

此外，还可以使用`copy`函数拷贝一个切片的内容到另一个切片，例如：

```
var numbers []int
numbers.append(numbers, 1, 2, 3, 4) // numbers : {1, 2, 3, 4}
var numbers1 := make([]int, len(numbers))
copy(number1, numbers) // 将numbers的内容拷贝到numbers1
```

# 集合 (Map)

Go语言的`Map`与其他很多语言一样，指的是键值对的集合。`Map`中的每一个元素包括一个`key`和一个`value`，一个`Map`中的各个`key`需要互不相同。`Map`的创建有两种常见的方式，一种是借助于`make`函数，一种是直接使用`map`关键字，例如：

```
m := make(map[string]int) // key的类型为string，value的类型为int
var m1 map[string]int
m2 := map[string]int{"one": 1, "two": 2}
```

直接使用`key`的值作为下标即可访问对应的元素，如果要添加元素，也可以直接通过赋值的方式添加，例如：

```
m["one"] = 1 // 添加元素
fmt.Println(m["one"]) //访问元素
```

如果要删除一个键值对，需要借助于`delete`函数，如下所示：

```
delete(m, "one") // 删除m["one"]
```

此外，`map`是无序的，因此其在迭代时的顺序是未定义的。

# 范围（Range）

在Go语言中，可以用`range`关键字配合`for`语句来实现对可迭代数据（例如切片slice、数组array、通道channel、集合map等）的遍历访问。`range`向外提供两个数据，代表`key`和`value`，在数组中表现为下标和数据，下面是一些例子：

```go
nums := []int{2, 3, 4}
sum := 0
for i, num := range nums {
	sum += num
	if num == 2 {
		fmt.Println("index:", i, "num:", num) // index: 0 num: 2
	}
}
```

上面的例子里，使用`range nums`创建了一个`nums`切片的`range`，它将在循环中迭代，每次迭代会向外给出两个值，即上面的`i`与`num`。在上面的例子里，`i`在整个循环过程中的值依次为`0, 1, 2`，`num`在整个循环过程中的值为`2, 3, 4`，即`nums[0], nums[1], nums[2]`。

如果不需要用到`key`或者`value`的值，则可以简单地使用`_`代替，以忽略它们。例如`for _, num := range nums`或者`for i, _ := range nums`。此外，如果要忽略的是`value`的值，也可以直接写成`for i := range nums`，这与`for i, _ := range nums`是等价的。

在`map`的遍历中使用`range`的例子如下所示：

```go
m := map[string]string{"a": "A", "b": "B"}
for k, v := range m {
	fmt.Println(k, v) // b 8; a A
}
for k := range m {
	fmt.Println("key", k) // key a; key b
}
```

# 指针（Pointer）

不同于Java等语言，Go语言中的参数传递、赋值等操作一律是值传递，而非引用传递，更准确地说，Go语言中并没有引用这一概念。与C语言类似，Go语言没有引用机制，只提供指针。Go语言的指针使用的相关语法也与C语言很相近，指针的类型为`*type`，获取一个变量的指针的方法是使用`&`，获取一个指针指向的变量的方法是使用`*`。比如对于变量`var a int`，要获取指向它的指针，可以使用`&a`（即取得`a`的内存地址）；对于指针变量`var b *int`，要获取它指向的变量，可以使用`*b`。下面是一个例子：

```go
func add2(n int) {
	n += 2
}

func add2ptr(n *int) {
	*n += 2
}

func main() {
	n := 5
	add2(n)
	fmt.Println(n) // 5
	add2ptr(&n)
	fmt.Println(n) // 7
}
```

通过上面的例子要说明的是，Go语言的函数传参是值传递，函数对参数值的修改并不会影响到函数外的变量的值，需要使用指针才能实现函数内对函数外变量的修改。另外，Go语言中存在`空`的概念，它的关键字是`nil`，每一个未初始化的指针的值就是`nil`，即空指针。

# 结构体（struct）

Go语言中并没有类（`class`）的概念，如果面向对象进行编程，通过`struct`会比较容易。在Go语言中，使用`type`关键字可以定义一个类型，`struct`属于一种`type`，因此`struct`的定义方法如下所示：

```go
type user struct {
    name   string
    passwd string
}
```

上面的语句定义了一个名为`user`的结构体，包括两个`string`类型的属性，分别名为`name`和`passwd`。

在创建结构体类型的变量时，语法与其他类型基本一样，但是可以通过`{}`的形式指定结构体变量属性的值：

```go
a := user{name: "wang", password: "1024"}
b := user{"wang", "1024"}
c := user{name: "wang"}
c.password = "1024"
var d user
d.name = "wang"
d.password = "1024"
```

未被赋值的属性将被初始化为相应的空值（例如数值类型被赋为`0`，指针类型被赋为`nil`），在访问结构体变量的属性时，使用`.`操作符，例如上面的`d.name`

值得注意的是，因为Go语言中没有引用的概念，因此结构体变量作为函数参数传递时，传给函数的是结构体变量的拷贝而不是引用。

在Go语言中，可以声明属于各个类型的方法，这样的方法可以通过`.`操作符访问，类似于Java中的类的方法。要声明一个属于某一类型的方法，需要在函数名之前加上这个类型或者它的指针类型。例如：

```go
type user struct {
	name     string
	password string
}

func (u user) checkPassword(password string) bool {
	return u.password == password
}

func (u *user) resetPassword(password string) {
	u.password = password
}
```

上面的`checkPassword`和`resetPassword`函数都可以用`.`操作符通过`user`型变量访问，例如：

```
a := user{name: "wang", password: "1024"}
a.resetPassword("2048")
a.checkPassword("2048")
```

# 接口（interface）

Go语言中提供接口类型，开发者可以自己定义接口类型，以降低代码模块之间的耦合度，提高代码复用性。接口的定义方式和结构体很类似，如下：

```go
type Phone interface {
    call() int
}
```

上面的代码定义了一个名为`Phone`的接口，任何具有`call() int`方法的类型都被视为实现了这个接口。与结构体不同，接口内的属性是一个个函数声明。

```go
type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() int {
    fmt.Println("I am Nokia, I can call you!")
    return 0
}

type IPhone struct {
}

func (iPhone IPhone) call() int {
    fmt.Println("I am iPhone, I can call you!")
    return 1
}
```

上面的`NokiaPhone`和`IPhone`两个结构体类型都有`call() int`方法，因此被视为实现了`Phone`接口，可以在任何需要`Phone`类型变量的地方使用。
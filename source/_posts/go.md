---
title: Go语言简单入门
date: 2018-09-27 21:46:51
categories:
    - 笔记
tags: 
    - Go
---

区块链中Chain Code(智能合约)支持Go、Java、NodeJs语言，主要是由Go语言编写的，Go语言的优势在于部署简单、并发性好、良好的语言设计等等。

<!-- more -->

## <font color = "#159957">变量和常量</font>

### <font color = "#159957">变量</font>

- Go使用保留字 ``var`` 声明变量，同其他语言不同的地方在于变量的类型在变量名的后面

    ``e.g.`` ``var a int``

- 变量声明和赋值是两个过程，但是可以连在一起，如果变量没有显式初始化，则被隐式赋予其类型的零值(zero value)

    ``e.g.`` ``a:= 15``

    ``p.s.`` 符号 ``:=`` 是短变量声明(short variable declaration)的一部分，只能在函数内使用

- Go的编译器对声明却未使用的局部变量（local variables）会报错，因此使用 ``空标识符（blank identifier）`` 即 ``_`` 来命名任何语法需要变量名但程序逻辑不需要的时候

- 大写字母开头的变量是可导出的，也就是其它包可以读取的，是公有变量；小写字母开头的就是不可导出的，是私有变量

    ``p.s.`` 大写字母开头的函数也是一样，相当于class中的带public关键词的公有函数；小写字母开头的就是有private关键词的私有函数

### <font color = "#159957">常量</font>

- 常量用 ``constant`` 命名，可以用 ``iota`` 生成枚举值

```GoLang
//第一个 iota 表示为 0，因此 a 等于 0，当 iota 再次在新的一行使用时，它的值增加了 1，因此 b 的值是 1。
const (
    a = iota
    b = iota
)
//也可省略重复的iota
const (
    a = iota
    b
)
```

## <font color = "#159957">类型</font>

### <font color = "#159957">内置类型</font>

内置类型分为数值类型、字符串类型、布尔类型

- 数值类型
    - 整数
        - 无符号 ``uint8``，``uint16``，``uint32``，``uint64``。``uint8``的别名是``byte`` 
        - 带符号 ``int8``，``int16``，``int32``，``int64``， ``int32`` 的别名是``rune``
    - 浮点数 ``float32`` 和 ``float64`` 两种（没有float类型），默认是 ``float64``
    - 复数 ``complex128``，``complex64``
- 字符串类型 ``string`` Go中的字符串都是采用UTF-8字符集编码。字符串是用一对双引号（""）或反引号（` `）括起来定义
- 布尔类型 ``bool`` 值是true或false，默认为false

Go的数字类型的长度由硬件决定，如 ``int`` 类型在32位硬件上是32位的，而在64位硬件上是64位的。

这些类型本质上是原始的类型，因此，当对这些值进行增加或者删除的时候，会创建一个新值。当把这些类型的值传递给方法或者函数时，传递的对应值的副本。

### <font color = "#159957">引用类型</font>

引用类型可以分为：

- 切片 ``slice``
- 映射 ``map``
- 通道 ``channel``
- 接口 ``interface``
- 函数 ``func``

引用类型之所以可以引用，是因为我们创建引用类型的变量，其实是一个 ``标头（header）`` 值，标头值里包含一个指针，指向底层的数据结构，<font color = "#9ACD32">当我们在函数中传递引用类型时，其实传递的是这个标头值的副本，它所指向的底层结构并没有被复制传递</font>，这也是引用类型传递高效的原因。

本质上，我们可以理解函数的传递都是值传递，只不过引用类型传递的是一个指向底层数据的指针，所以我们在操作的时候，可以修改共享的底层数据的值，进而影响到所有引用到这个共享底层数据的变量。

### <font color = "#159957">结构类型</font>

    通过保留字 ``type`` 和 ``struct`` 声明新的类型，作为其它类型的属性或字段的容器

```GoLang
// 声明一个新的类型
type person struct {
	name string
	age int
}

type Student struct {
	person  // 匿名字段，那么默认Student就包含了person的所有字段，也叫嵌入字段
    speciality string
    int    // 内置类型也可以作为匿名字段
}

//还可以创建一个简单类型，新类型foo作用跟int一样
type foo int

func main() {
//1. 赋值初始化
var tom person
tom.name, tom.age = "Tom", 18
//2. 通过field:value的方式初始化，这样可以任意顺序
bob := person{age:25, name:"Bob"}
//3. 按照顺序提供初始化值
paul := person{"Paul", 43}
}

// 初始化一个学生
mark := Student{person:person{"Mark", 25}, speciality:"Computer Science"}
// 访问字段
fmt.Println("Her name is ", mark.name)
// 修改匿名内置类型字段
mark.int = 1
```

若struct与其匿名struct中有字段名重复，<font color = "#9ACD32">最外层的优先访问</font>。即上例中如果person里面有一个字段叫做phone，而Student也有一个字段叫做phone，则通过 Student.phone 访问的时候，是访问Student里面的字段，而不是human里面的字段。


## <font color = "#159957">控制结构</font>

### <font color = "#159957">if-else</font>

- Go的 ``if-else`` 语句中左括号必须强制跟if、else在同一行
```GoLang
//下面的语法在 Go 中是非法的
if err != nil
{ //"{"必须同if在同一行
    return err
}
```

### <font color = "#159957">for</font>

- Go的 ``for`` 循环有三种格式
```GoLang
for initialization; condition; post { //和C的for一样，不需要括号包围，大括号强制要求，左括号必须跟post在同一行
// for循环这三部分都可以省略
// condition是一个布尔表达式（boolean expression），其值在每次循环迭代开始时计算
}  
for condition { } //和 while 一样
for { } //和 C 的 for(;;) 一样（死循环）
```

- 由于 Go 没有逗号表达式，而 ++ 和 – 是语句而不是表达式，如果要在 for 中执行多个变量，应当使用 平行赋值。
```GoLang
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 { //平行赋值
    a[i], a[j] = a[j], a[i] //这里也是
}
```

### <font color = "#159957">range</font>

- 保留字 ``range`` 是个迭代器，可用于循环``slice``、``array``、``string``、``map`` 和 ``channel``。当被调用的时候，从它循环的内容中返回一个键值对。基于不同的内容，range 返回不同的东西。当对 slice 或者 array 做循环时，range 返回序号作为键，这个序号对应的内容作为值。
```GoLang
//创建一个字符串的slice
list := []string{"a", "b", "c", "d", "e", "f"}  
//用 range 对其进行循环。每一个迭代，range 将返回 int 类型的序号，string 类型的值，以 0 和“a”开始
for k, v := range list { 
    // 对 k 和 v 做想做的事情 
}
```

## <font color = "#159957">函数、方法、接口</font>

### <font color = "#159957">函数</font>

Go 语言通过关键字 ``func`` 定义函数

 ```GoLang
 func (r receiverType) funcName (input1 type1, intput2 type2) (output1, output2 type3) {
     
     return output1, output2
} 
 ```

#### <font color = "#159957">多参和变参</font>

函数支持返回多个值，也支持变参

```GoLang
// 返回值可以不声明变量，直接返回类型
func SumAndProduct(A, B int) (int, int) {
	return A+B, A*B
}
// 若命名了返回值的变量，返回时可以不带上变量名
func SumAndProduct(A, B int) (add int, Multiplied int) {
	add = A+B
	Multiplied = A*B
	return
}

// arg ...int告诉Go这个函数接受不定数量的参数，且这些参数都是int类型
func myfunc(arg ...int) {
    // 在函数体中，变量arg是一个int类型的slice
    for _, n := range arg {
	    fmt.Printf("And the number is: %d\n", n)
    }
}
```

#### <font color = "#159957">作用域</font>

在 Go 中，定义在函数外的变量是全局的，那些定义在函数内部的变量，对于函数来说是局部的。如果命名覆盖(一个局部变量与一个全局变量有相同的名字)，在函数执行的时候，局部变量将覆盖全局变量

#### <font color = "#159957">传值与传指针</font>

当我们传一个参数值到被调用函数里面时，实际上是传了这个值的一份copy，当在被调用函数中修改参数值的时候，调用函数中相应实参不会发生任何变化，因为数值变化只作用在copy上。

传指针可以修改对应变量值，此时参数仍然是按copy传递的，只是copy的是一个指针

#### <font color = "#159957">延迟语句</font>

使用 ``defer`` 定义延迟语句，在 ``defer`` 后指定的函数会在函数退出前调用

```GoLang
func ReadWrite() bool {
    file.Open("file")
    //打开资源遇到错误返回前需要关闭资源，不然会造成资源泄露
	defer file.Close()
	if failureX {
		return false
	}
	if failureY {
		return false
	}
	return true
}
```

如果有很多调用defer，那么defer是采用**后进先出**模式

```GoLang
// 后进先出，所以如下代码会输出4 3 2 1 0
for i := 0; i < 5; i++ {
	defer fmt.Printf("%d ", i)
}
```

#### <font color = "#159957">函数作为值</font>

在Go中函数也是一种变量

```GoLang
//声明了一个函数类型
type testInt func(int) bool 

//定义一个返回值是布尔值的函数
func isOdd(integer int) bool {
	if integer%2 == 0 {
		return false
	}
	return true
}

// 声明的函数类型在这个地方当做了一个参数
func filter(slice []int, f testInt) []int {
	var result []int
	for _, value := range slice {
		if f(value) {
			result = append(result, value)
		}
	}
	return result
}

func main(){
	slice := []int {1, 2, 3, 4, 5, 7}
    fmt.Println("slice = ", slice)
     // 函数当做值来传递
	odd := filter(slice, isOdd)
	fmt.Println("Odd elements of slice are: ", odd)
    // 匿名函数
    a := func() {}
    // 调用匿名函数
    a()
}
```

#### <font color = "#159957">init函数和main函数</font>

Go里面有两个保留的函数：``init`` 函数（能够应用于所有的package）和 ``main`` 函数（只能应用于package main）。这两个函数在定义时不能有任何的参数和返回值。

程序的初始化和执行都起始于 ``main`` 包。如果 ``main`` 包还导入了其它的包，那么就会在编译时将它们依次导入，同一个包只会被导入一次。当一个包被导入时，如果该包还导入了其它的包，那么会先将其它包导入进来，然后再对这些包中的包级常量和变量进行初始化，接着执行这些包的 ``init`` 函数（如果有的话），依次类推。等所有被导入的包都加载完毕了，就会开始对main包中的包级常量和变量进行初始化，然后执行main包中的 ``init`` 函数（如果存在的话），最后执行 ``main`` 函数。

#### <font color = "#159957">内置函数</font>

- ``close`` 用于 channel 通讯，使用它来关闭 channel
- ``delete`` 用于在 map 中删除实例
- ``len`` 和 ``cap`` 可用于不同的类型，len 用于返回字符串、slice 和数组的长度， cap获取最大容量。
> Go 语言的字符串采用 utf8 编码，中文汉字通常需要占用 3 个字节，英文只需要 1 个字节。len() 函数得到的是字节的数量
- ``new`` 用于各种类型的内存分配
- ``make`` 用于内建类型（map、slice 和 channel）的内存分配
- ``copy`` 用于复制 slice
- ``append`` 用于追加 slice
- ``panic`` 和 ``recover`` 用于异常处理机制
- ``print`` 和 ``println`` 是底层打印函数，可以在不引入 fmt 包的情况下使用。它们主要用于调试
- ``complex``、``real`` 和 ``imag`` 全部用于处理复数

fmt.Printf函数对一些表达式产生格式化输出，各个参数的格式取决于“转换字符”（conversion character），形式为百分号后跟一个字母。举个例子，%d表示以十进制形式打印一个整型操作数，而%s则表示把字符串型操作数的值展开。

### <font color = "#159957">方法</font>

Go语言中 ``方法`` 和 ``函数`` 是不同的概念，关键字 ``func`` 和函数名之间的参数叫 ``接受者(receiver)`` ，如果一个函数有接受者，这个函数就被称为 ``方法`` 。 方法可以继承和重写，这是函数没有的特性
 
#### <font color = "#159957">方法重写</font>

下面的代码会提示 ``'area' redeclared in this package``

```GoLang
type Rectangle struct {
	height, width float64
}

type Circle struct {
	radius float64
}

func area(r Rectangle) float64  {
	return r.height * r.width
}

func area(c Circle) float64{
	return c.radius * c.radius * math.Pi
}
```

方法可以解决上述问题

```GoLang
func (r Rectangle) area() float64{
	return r.height * r.width
}

func (c Circle) area() float64 {
	return c.radius * c.radius * math.Pi
}

func main() {
	r := Rectangle{1.0, 3.0}
	c := Circle{2.0}
	fmt.Printf("%f", r.area())
	fmt.Printf("%f", c.area())
}
```

#### <font color = "#159957">方法继承</font>

如果匿名字段实现了一个method，那么包含这个匿名字段的struct也能调用该method

```GoLang
type Human struct {
    name string
    age int
}

type Student struct {
    Human //匿名字段
    school string
}

//匿名字段实现了一个method
func (h *Human) SayHi() {
	fmt.Printf("%s, %d", h.name, h.age)
}

func main() {
	mark := Student{Human{"Mark", 25}, "MIT"}
    // 包含这个匿名字段的struct也能调用该method
    mark.SayHi()
}
```

### <font color = "#159957">接口</font>

interface就是一组抽象方法的集合，它必须由其他非interface类型实现，而不能自我实现。

> Java 语言需要在类的定义上显式实现了某些接口，才可以说这个类具备了接口定义的能力。但是 Go 语言的接口是隐式的，只要结构体上定义的方法在形式上（名称、参数和返回值）和接口定义的一样，那么这个结构体就自动实现了这个接口。

```GoLang
type Human struct {
	name string
	age  int
}

type Student struct {
	Human
	school string
}

type Employee struct {
	Human
	company string
}
//Human实现SayHi方法
func (h Human) SayHi() {
	fmt.Printf("%s, %d", h.name, h.age)
}

func (h Human) Sing() {
	fmt.Printf("la la la ")
}
//Employee重载Human的SayHi方法
func (e Employee) SayHi() {
	fmt.Printf("%s, %s", e.name, e.company)
}

type Men interface {
	SayHi()
	Sing()
}

func main() {
	mike := Student{Human{"Mike", 25}, "MIT"}
    tom := Employee{Human{"Tom", 36}, "Tencent"}
    //定义Men类型的变量i
	var i Men
    //i能存储Student
	i = mike
	i.SayHi()
    i.Sing()
    //i也能存储Employee
	i = tom
    i.SayHi()
    //定义了slice
	x := []Men{mike, tom}
	for _, v := range x {
		v.SayHi()
	}
}
```

#### <font color = "#159957">空interface</font>

一个函数把interface{}作为参数，那么他可以接受任意类型的值作为参数，如果一个函数返回interface{},那么也就可以返回任意类型的值

```GoLang
// 定义Element为空接口，可以存储任意类型的数值
type Element interface {}

func main() {
	list := []Element{1, "Hello"} // 空接口可以存储任意类型的数值
	for index, element := range list {
        // Comma-ok断言：value, ok = element.(T)，value就是变量的值，ok是一个bool类型，element是interface变量，T是断言的类型
        if value, ok := element.(int); ok { //if里面允许初始化变量
            fmt.Printf("%d,%d\n", index, value)
        } else if value, ok := element.(string); ok {
            fmt.Printf("%d,%s\n", index, value)
        }
        // 可以用switch语句
		// switch value := element.(type) {
		// case int:
		// 	fmt.Printf("%d,%d\n", index, value)
		// case string:
		// 	fmt.Printf("%d,%s\n", index, value)
		// }
	}
}
```

## <font color = "#159957">array、slice 和 map</font>

### <font color = "#159957">array</font>

- ``array`` 由 ``[n]<type>`` 定义，``n`` 标示 array 的长度，而 ``<type>`` 标示希望存储的内容的类型。
```GoLang
//默认值都是0
var a [3]int
//使用复合声明来初始化值
a := [3]int{1, 2, 3} 
//使用[...]语法糖，编译器会自动统计元素的个数
a := [...]int{1, 2, 3}
```

### <font color = "#159957">slice</font>

``slice`` 是引用类型，称作 ``切片``，是指向 ``array`` 的指针，类似Java ArrayList，ArrayList 的内部实现也是一个数组。当数组容量不够需要扩容时，就会换新的数组，还需要将老数组的内容拷贝到新数组。

使用 make 函数创建切片，需要提供三个参数，分别是切片的类型、切片的长度和容量。其中第三个参数是可选的，如果不提供第三个参数，那么长度和容量相等，也就是说切片的满容的。

```GoLang
// 使用长度声明一个字符串切片，长度和容量都是5
slice := make([]string, 5)

// 使用长度和容量声明整型切片，长度是3，容量是5
slice := make([]int, 3, 5)

//通过 a[n:m] 的方式创建 ``slice``，指向变量 a，具有元素 n 到 m-1。长度为 m-n
var array [m]int
slice := array[0:n]
// len(slice)== n 
// cap(slice)== m 
// len(array)== cap(array)== m 

// a[0:n] 可以简写为 a[:n]
// a[0:len(a)] 可以简写为 a[:]
```

#### <font color = "#159957">”零切片“、”空切片“、”nil切片“</font>

```GoLang
func main() {
	var zeroSlice = make([]*int,5)  //零切片，值是各类型零值
	var emptySlice = []int{} //空切片
	var nilSlice []int   //nil切片
	fmt.Println(zeroSlice) // [<nil> <nil> <nil> <nil> <nil>]
	fmt.Println(emptySlice) // []
	fmt.Println(nilSlice) // []
	fmt.Println(emptySlice == nil) //false
	fmt.Print(nilSlice == nil) //true
}
```
空切片和nil切片区别不大，官方提倡使用nil切片
> They are functionally equivalent—their len and cap are both zero—but the nil slice is the preferred style.

#### <font color = "#159957">声明数组和声明切片的不同</font>

```GoLang
array := [3]int{10, 20, 30}  // 创建有 3 个元素的整型数组
slice := []int{10, 20, 30} // 创建长度和容量都是 3 的整型切片
```

#### <font color = "#159957">使用内置函数 ``append`` 和 ``copy`` 扩展 slice</font>
- ``func append(slice []Type, elems ...Type) []Type`` 函数 append 向 slice s 追加零值或其他值，并且返回追加后的新的与 s 相同类型的 slice。如果 s 没有足够的容量存储追加的值，append 分配一个足够大的、新的 slice 来存放原有 slice 的元素和追加的值。因此，<font color = "#9ACD32">返回的 slice 可能指向不同的底层 array</font>
```GoLang
slice0 := []int{0,1}
slice1 := append(slice0, 2)
// ...是必须的，加上省略号相当于把slice1包含的所有元素打散后传入
slice3 := append(slice0, slice1...)
```

- ``func copy(dst, src []Type) int`` 从源 slice src 复制元素到目标 dst，并且返回复制的元素的个数。源和目标可能重叠。<font color = "#9ACD32">复制的数量是 len(src) 和 len(dst) 中的最小值</font>
```GoLang
var a = [...]int{0, 1, 2, 3}
var s = make([]int, 3)
i := copy(s, a[:1]) //i==1, s==[]int{0,0,0}
j := copy(s, a[:])  //i==3, s==[]int{0,1,2}
```

copy函数是深拷贝，直接赋值是浅拷贝

```GoLang
var a = make([]int, 3)
var b = a //这是浅拷贝，共享底层数组，a的值变化b也会变
```

> 浅拷贝(shallowCopy)只是增加了一个指针指向已存在的内存地址，深拷贝(deepCopy)是增加了一个指针并且申请了新的内存，使新增的指针指向新的内存

### <font color = "#159957">map</font>

``map`` 映射是一种数据结构，用于存储一系列无序的键值对，格式 ``map[<from type>]<to type>``

> 数组切片让我们具备了可以操作一块连续内存的能力，它是对同质元素的统一管理。而字典则赋予了不连续不同类的内存变量的关联性，它表达的是一种因果关系，字典的 key 是因，字典的 value 是果。如果说数组和切片赋予了我们步行的能力，那么字典则让我们具备了跳跃的能力。

```GoLang
// 创建初始化
var dict map[string]string //nil 映射，不能赋值
monthdays := map[string]int{
    "Jan": 31, "Feb": 28,  //逗号是必须的
}
monthdays2 := map[string]int{"Jan": 1, "Feb": 2}

// 赋值
monthdays["Mar"] = 31

// 判断键是否存在
v, ok := monthdays["Mar"]

// 使用 range 遍历
year := 0
for _, days := range monthdays { //键没有使用，因此用 _, days
    year += days
}

// 删除元素
delete(monthdays, "Mar")
```

Go map没有提供Java Map的 keySet()、values()方法，需手动实现

```GoLang
func main() {
	fruits := map[string]int{"apple": 1, "banana": 2, "orange": 3}
	keys := make([]string, 0, len(fruits))
	values := make([]int, 0, len(fruits))
	for key, value := range fruits {
		keys = append(keys, key)
		values = append(values, value)
	}
}
```

## <font color = "#159957">并发</font>

### <font color = "#159957">goroutine和channel</font>

Go 使用 ``channel`` 和 ``goroutine`` 开发并行程序。 ``goroutine`` 是通过 Go的runtime管理的一个线程管理器。``goroutine`` 通过 ``go`` 关键字实现， ``channel`` 通过 ``chan`` 关键字实现，``goroutine`` 之间通过 ``channel`` 通信，默认情况下，``channel`` 接收和发送数据都是阻塞的，除非另一端已经准备好，这样就使得Goroutines同步变的更加的简单，而不需要显式的lock。虽然goroutine 是并发执行的，但是它们并不是并行运行的。如果不告诉Go 额外的东西，同一时刻只会有一个goroutine 执行。利用 ``runtime.GOMAXPROCS(n)`` 可以设置goroutine 并行执行的数量

```GoLang
func ready(w string, sec int) { 
	time.Sleep(time.Duration(sec) * time.Second) 
	fmt.Println(w, "is ready!") 
}

func main() {
	ready("Tea", 2) //普通函数调用
	go ready("Tea", 2) //ready() 作为goroutine 运行

	//必须使用make 创建channel
	ci := make(chan int) //默认的非缓存类型的channel
	cs := make(chan string)
	cf := make(chan interface{})

	//channel通过操作符<-来接收和发送数据
	ch <- v    // 发送v到channel ch.
	v := <-ch  // 从ch中接收数据，并赋值给v

	//指定缓冲大小
	ch:= make(chan bool, 4)//创建了可以存储4个元素的bool 型channel
}
```

### <font color = "#159957">Buffered Channels</font>

``ch := make(chan type, value)`` 当 value = 0 时，channel 是无缓冲阻塞读写的，当value > 0 时，channel 有缓冲、是非阻塞的，直到写满 value 个元素才阻塞写入。

### <font color = "#159957">range和close</font>

``for i := range ch`` 能够不断的读取channel里面的数据，直到该channel被显式的关闭。生产者通过内置函数 ``close`` 关闭channel，在消费方可以通过语法 ``v, ok := <-ch`` 测试channel是否被关闭，如果ok返回false，那么说明channel已经没有任何数据并且已经被关闭。

### <font color = "#159957">select</font>

如果存在多个channel，可以通过 ``select`` 监听channel。``select`` 默认是阻塞的，只有当监听的channel中有发送或接收可以进行时才会运行，当多个channel都准备好的时候，``select`` 是随机的选择一个执行的

```GoLang
select {
case i := <-c:
	// use i
default:
	// 当c阻塞的时候执行这里
}
```

## <font color = "#159957">学习资料</font>

- [《学习Go语言》](https://mikespook.com/learning-go/)
- [《Go语言实战》](https://book.douban.com/subject/25858023/)
- [《Go Web 编程》](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/preface.md)
- [《Go语言圣经》](http://shouce.jb51.net/gopl-zh/ch1/ch1-05.html)
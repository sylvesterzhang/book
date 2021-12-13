---
layout: post
title: go语言基础
tags: go
categories: backend
---

概述、变量、常量、运算符和函数、导包、指针、defer、数组、切片、map、type使用、面向对象、反射、chanel、协程、json操作、随机数、网络编程、读取文件、beego



**概述**

`1`特性：

- 自动垃圾回收

- 更丰富的内置类型 

- 函数多返回值 

- 错误处理 

- 匿名函数和闭包

-  类型和接口 

-  并发编程 

-  反射

- 语言交互性

  

`2`编译

linux可执行文件

```
set GOARCH=amd64 set GOOS=linux go build
```

windows可执行文件

```
set GOARCH=386 set GOOS=windows go build
```

---

**变量**

相当于时一块数据存储空间的命名

1）变量声明

```go
var v1 int
var v2 string
var v3 [10]int // 数组
var v4 []int // 数组切片
var v5 struct {
 f int
}
var v6 *int // 指针
var v7 map[string]int // map，key为string类型，value为int类型
var v8 func(a int) int
```

var关键字的另一种用法是可以将若干个需要声明的变量放置在一起

```go
var (
 v1 int
 v2 string
) 
```

单行声明

```go
var a,b=1,2
```

2）变量初始化

```go
var v1 int = 10 // 正确的使用方式1
var v2 = 10 // 正确的使用方式2，编译器可以自动推导出v2的类型
v3 := 10 // 正确的使用方式3，编译器可以自动推导出v3的类型，这样的方式不可声明全局变量
```

变量一旦被声明后必须使用，否则无法编译

---

**常量**

`1`常量特点

- 常量的值不可被改变
- 常量不复制会自动赋值上一行的表达式
- 常量定义后不使用，编译器不会报错

```go
package main

import (
	"fmt"
)

func main() {

	const(
		sping =10
		summer
		autumn = 20
		winter
	)
	fmt.Println(sping,summer,autumn,winter)

}

```

`2`iota枚举

以一组相似规则初始化常量，且只可在const中使用

```go
package main

import "fmt"

func main(){

	const (
		a=iota
		b
		c
		d
	)
	fmt.Printf("a=%d,b=%d,c=%d,d=%d",a,b,c,d)//a=0,b=1,c=2,d=3
}

```

```go
package main

import "fmt"

func main(){//方法后的大括号不可以换行

	const (
		a=iota
		b,c=iota,iota//如果定义的枚举常量写在同一行值相同，换一行加一
		d,e
	)
	fmt.Printf("a=%d,b=%d,c=%d,d=%d,e=%d",a,b,c,d,e)//a=0,b=1,c=1,d=2,e=2
}

```

```go
package main

import "fmt"

var z=1
func main() {

	const(
		a,b=iota+1,iota+2 //iota=0 1,2
		c,d				  //iota=1 3,4
		e,f=iota*2,iota*3 //iota=2 4,6
		g,h				  //iota=3 6,9
	)
	fmt.Printf("a=%d,b=%d,c=%d,d=%d,e=%d,f=%d,g=%d,h=%d",a,b,c,d,e,f,g,h)
    //a=1,b=2,c=2,d=3,e=4,f=6,g=6,h=9
}
```

---

**运算符和函数**

`1`算术运算符

| 算数符 | 含义                                         |
| ------ | -------------------------------------------- |
| +      | 加                                           |
| -      | 减                                           |
| *      | 乘                                           |
| /      | 除                                           |
| %      | 取余                                         |
| ++     | 自增，仅后自增，不可运用在表达式中，如a:=b++ |
| --     | 自减，仅后自减，不可运用在表达式中，如a:=b-- |

`2`数据类型转换

```go
package main

import "fmt"

func main(){

	var a int32 = 1;
	var b int64 = 2;
	c := int64(a) + b//将32位的int转换成64为的int
	fmt.Print(c)
	
}

```

`3`有返回值的函数

```go
package main

import "fmt"

func main() {
	a,b :=foo()
	fmt.Printf("a=%d,b=%d",a,b)
}

func foo()(int,int){
	return 1,2
}

```

```go
package main

import "fmt"

func main() {
	var a int;
	var b int;
	a,b =foo()
	fmt.Printf("a=%d,b=%d",a,b)
}

func foo()(a int,b int){
	a = 1
	b = 2
	return
}

```

```go
package main

import "fmt"

func main() {
	var a int;
	var b int;
	a,b =foo()
	fmt.Printf("a=%d,b=%d",a,b)
}

func foo()(a ,b int){
	a = 1
	b = 2
	return
}

```

---

**导包**

`1`导包基本过程

1）定义包

创建文件夹lib1，lib2

```go
package lib1

import "fmt"

func Foo1()  {
	fmt.Println("lib1")
}
```

```go
package lib2

import "fmt"

func Foo2()  {
	fmt.Println("lib2")
}

```

```go
package lib2

import "fmt"

func Foo3()  {
	fmt.Println("lib3")
}
```

2）导包调用

```go
package main


import (
	"awesomeProject1/lib1"
	"awesomeProject1/lib2"
)
func main() {
	lib1.Foo1()
	lib2.Foo2()
	lib2.Foo3()
}

```

`2`导包方式

```go
import (
    _ "awesomeProject1/lib1" //匿名导入，会执行包里的init()
	lib "awesomeProject1/lib2" //为导入的包取别名，通过别名调用包内方法
    . "awesomeProject1/lib2" //将包内方法导入当前路径，直接调用方法
)
```

---

**指针**

```go
import "fmt"

func main() {

	a := 1
	b := 2
	swap(&a,&b)
	fmt.Printf("a=%d,b=%d",a,b) //a=2,b=1

}

func swap(pa *int,pb *int)  {
	var temp int
	temp = *pa //1
	fmt.Println(temp)
	*pa = *pb
	*pb = temp
}
```

声明指针用\*，取某个对象的指针用&，通过指针获取该指针的对象用\*



```go
package main

import "fmt"

type student struct {
	name string
	age  int
}

func main() {

	m := make(map[string]*student)
	stus := []student{
		{name: "pprof.cn", age: 18},
		{name: "测试", age: 23},
		{name: "博客", age: 28},
	}

	for _, stu := range stus {
		m[stu.name] = &stu
	}

	for k, v := range m {
		fmt.Println(k,v)
		fmt.Println(k, "=>", v.name)
	}
	/**
	pprof.cn &{博客 28}
	pprof.cn => 博客
	测试 &{博客 28}
	测试 => 博客
	博客 &{博客 28}
	博客 => 博客
	 */
}


```

不要遍历对象时用指针，和预想的不一致

---

**defer**

```go
package main

func main() {
	foo()
	//aaaaaaaaaaaaaaa
	//最后执行……
}

func foo()(int)  {
	defer println("最后执行……")
	return foo1()
}

func foo1()(int)  {
	println("aaaaaaaaaaaaaaa")
	return 1
}


```

---

**数组**

```go
package main

func main() {
	//不定长度数组
	array1 := []int{1,2,3}
	//定长数组
	array2 := [10]int{}
	
	printArray(array1)
	printArray1(array2)
}

func printArray(array []int)  {
	for _,value := range array{
		println(value)
	}
}

func printArray1(array [10]int)  {
	for _,value := range array{
		println(value)
	}
}

```

把数组作为参数在方法中传递，对于动态数组传递的是引用，对于固定长度的数组，通过值拷贝方式传递

---

**切片**

1）声明

```go
package main

import "fmt"

func main() {
    //字面值方式
	slice := []int{1,2}
	var slice1 []int
    
    //内置函数make
	slice1 = make([]int,2)
	slice2 := make([]int,2)

	fmt.Printf("%v",slice)
	fmt.Printf("%v",slice1)
	fmt.Printf("%v",slice2)
    
    // 创建一个整型切片
	// 其长度为 3 个元素，容量为 5 个元素
	slice := make([]int, 3, 5)
	fmt.Println(slice)
	//长度不可以大于容量

}
```

注意

    // 注意和字符串数组区别([]和[...]), 下面是声明数组
    arr := [...]string{"Red", "Blue", "Green", "Yellow", "Pink"}
2）扩容、截取、拷贝

```go
package main

import "fmt"

func main() {
	slice := []int{1,2}
	slice = append(slice, 3)//扩容

	s3 := make([]int,2)
	copy(s3,slice)//将slice中的数据拷贝到s3中

	fmt.Printf("%v",slice)
	fmt.Printf("%v",slice[1:])//截取
	fmt.Printf("%v",s3)//拷贝
}
```

---

**Map**

1）声明

```go
package main

import "fmt"

func main() {
	//第一种声明方式
	var mymap1  map[string]string
	mymap1 =make(map[string]string,10)
	initmap(mymap1)
	fmt.Printf("%v",mymap1)

	//第二种声明方式
	mymap2 := make(map[string]string,10)
	initmap(mymap2)
	fmt.Printf("%v",mymap2)

	//第三种声明方式,字面值方式
	mymap3 := map[string]string{
		"1":"java",
		"2":"python",
		"3":"C++",
	}
	fmt.Printf("%v",mymap3)

}

func initmap(map1 map[string]string)  {
    //传递的是引用
	map1["1"] = "java"
	map1["2"] = "python"
	map1["3"] = "C++"
}
```

2）使用

```go
package main

import "fmt"

func main() {

	mymap3 := map[string]string{
		"1":"java",
		"2":"python",
		"3":"C++",
	}
	fmt.Printf("%v",mymap3)

	//新增
	mymap3["4"] = "go"

	//遍历
	for key,value := range mymap3{
		fmt.Println(key)
		fmt.Println(value)
	}

	//删除
	delete(mymap3,"4")

	//修改
	mymap3["4"] = "c"

}
```

---

**type使用**

1）给某个类型起别名

```go
package main

import "fmt"

type myint int

func main() {

	var a myint
	a =1
	fmt.Println(a)

}
```

2）定义结构体

```go
import "fmt"

type Book struct {
	name string
}

func main() {

	book := Book{}
	book.name = "111"
	fmt.Println(book)
}
```

---

**面向对象**

1）对象的定义

```go
package main

import "fmt"

//定义对象
type Cat struct {
	color string
}

//定义方法
func (this *Cat) getColor() string {
	return this.color
}

func (this *Cat) setColor(color string)  {
	this.color = color
}

func (this *Cat) Show()  {
	fmt.Println("this cat's color is ",this.color)
}

func main() {
	cat :=Cat{}
	cat.setColor("red")
	cat.Show()
}
```

对象的方法或属性大写表示在包外可以访问，小写表示仅在本包内可访问（封装）

2）继承

```go
package main

import "fmt"

type Cat struct {
	color string
}

func (this *Cat) getColor() string {
	return this.color
}

func (this *Cat) setColor(color string)  {
	this.color = color
}

func (this *Cat) Show()  {
	fmt.Println("this cat's color is ",this.color)
}

type tomCat struct {
	Cat
}
func main() {
	cat := tomCat{}
	cat.setColor("red")
	cat.Show()
}
```

3）多态

```go
package main

import "fmt"

type Animal interface {
	getColor() string
	setColor(color string)
	Show()
}

type Cat struct {
	color string
}

func (this *Cat) getColor() string {
	return this.color
}

func (this *Cat) setColor(color string)  {
	this.color = color
}

func (this *Cat) Show()  {
	fmt.Println("this cat's color is ",this.color)
}

func main() {
	var cat Animal
	cat = &Cat{}
	cat.setColor("red")
	cat.Show()
}
```

类实现接口中的全部方法就可以用接口类型的指针指向子类型的对象

4）万能接口

```go
package main

import "fmt"

func test(a interface{})  {
	fmt.Println(a)
}
func main() {
	test(1)
	test("aaaa")
}
```

```go
package main

import "fmt"

func test(a interface{})  {
	//断言
	value ,ok :=a.(string)

	fmt.Println(value,ok)
}
func main() {
	test(1) //false
	test("aaaa")//aaa true
}
```

---

**反射**

`1`获取类型及属性

```go
package main

import (
	"fmt"
	"reflect"
)

type User struct {
	Id int
	Name string
	Age int
}

func DofieldAndMethod(arg interface{})  {
	argType := reflect.TypeOf(arg)
	argValue :=reflect.ValueOf(arg)
	fmt.Println("type:",argType)
	fmt.Println("value:",argValue)

	for i := 0;i<argType.NumField();i++{
		field := argType.Field(i)
		value := argValue.Field(i).Interface()
		fmt.Println(field.Name," ",field.Type,"=",value)
	}
}
func main(){
	user := User{
		1,
		"zhang",
		22,
	}
	DofieldAndMethod(user)
}
```

`2`标签

```go
package main

import (
	"fmt"
	"reflect"
)

type User struct {
	Id int `info:"id" doc:"内码"`
	Name string `info:"姓名" doc:"用户姓名"`
	Age int `info:"年龄" doc:"用户年龄"`
}

func findTag(arg interface{})  {
	t := reflect.TypeOf(arg).Elem()
	for i :=0;i<t.NumField();i++{
		taginfo := t.Field(i).Tag.Get("info")
		tagdoc := t.Field(i).Tag.Get("doc")
		fmt.Println("info =",taginfo)
		fmt.Println("doc =",tagdoc)
	}
}
func main(){
	user := User{}
	findTag(&user)
}

/**
info = id
doc = 内码
info = 姓名
doc = 用户姓名
info = 年龄
doc = 用户年龄
 */
```

---

**Channel**

`1`定义和取值

```go
package main

import (
	"fmt"
	"time"
)

func main(){
	c := make(chan int)
	go func() {
		time.Sleep(time.Second)
		c <- 666
		c <- 111
	}()
	num := <-c //未拿到值，这里会阻塞
	num1 := <-c
	fmt.Println(num)//666
	fmt.Println(num1)//111
}
```

```go
package main

import (
	"fmt"
)

func main() {

	ch1 := make(chan int)
	ch2 := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			ch1 <- i
		}
		close(ch1)
	}()
	go func(){
		for{
			i, ok := <-ch1 // 通道关闭后再取值ok=false
			if !ok {
				break
			}
			ch2 <- i * i
		}
		close(ch2)
	}()
	for i:=range ch2{
		fmt.Println(i)
	}
}
```



`2`Channel的关闭

```go
package main

import "fmt"

func main(){
	c := make(chan int)
	go func() {
		for i :=0;i<5;i++{
			c <-i
		}
		close(c)
	}()

	for{
		if data,ok := <-c; ok{
			fmt.Println(data)
		}else{
			break
		}
	}
	fmt.Println("finished...")
	/**
	0
	1
	2
	3
	4
	finished...
	*/
}
```

关闭channel后无法向其发送数据，但可接受数据

```go
package main

import "fmt"

func main(){
	c := make(chan int)
	go func() {
		for i :=0;i<5;i++{
			c <-i
		}
		close(c)
	}()

    //和上面代码等效
	for data := range c{
		fmt.Println(data)
	}

	fmt.Println("finished...")
}
```



`3`只写和只读

```go
package main

import "fmt"

func main()  {
   //只写
   ch1 := make(chan <- int)
   ch1 <- 1

   //只读
   ch2 := make(<- chan int)
   a := <-ch2 //会报错
   fmt.Println(a)
}

```



---

**协程**

`1`协程定义

```go
package main

import (
	"fmt"
	"sync"
)

var wg sync.WaitGroup
func main() {

	for i := 0; i < 10; i++ {
		wg.Add(1) // 启动一个goroutine就登记+1
		go hello(i)
	}
	wg.Wait() // 等待所有登记的goroutine都结束
}

func hello(i int) {
	defer wg.Done() // goroutine结束就登记-1
	fmt.Println("Hello Goroutine!", i)
}
```

`2`协程池

```go
package main

import (
	"fmt"
	"time"
)

func main() {

	//创建一个Task
	t := NewTask(func() error {
		fmt.Println(time.Now())
		time.Sleep(time.Second)
		return nil
	})

	//创建一个协程池,最大开启3个协程worker
	p := NewPool(1)

	//开一个协程 不断的向 Pool 输送打印一条时间的task任务
	go func() {
		for i:=0;i<10;i++{
			p.EntryChannel <- t
		}
	}()

	//启动协程池p
	p.Run()

}
/* 有关Task任务相关定义及操作 */
//定义任务Task类型,每一个任务Task都可以抽象成一个函数
type Task struct {
	f func() error //一个无参的函数类型
}

//通过NewTask来创建一个Task
func NewTask(f func() error) *Task {
	t := Task{
		f: f,
	}

	return &t
}

//执行Task任务的方法
func (t *Task) Execute() {
	t.f() //调用任务所绑定的函数
}

/* 有关协程池的定义及操作 */
//定义池类型
type Pool struct {
	//对外接收Task的入口
	EntryChannel chan *Task

	//协程池最大worker数量,限定Goroutine的个数
	worker_num int

	//协程池内部的任务就绪队列
	JobsChannel chan *Task
}

//创建一个协程池
func NewPool(cap int) *Pool {
	p := Pool{
		EntryChannel: make(chan *Task),
		worker_num:   cap,
		JobsChannel:  make(chan *Task),
	}

	return &p
}

//协程池创建一个worker并且开始工作
func (p *Pool) worker(work_ID int) {
	//worker不断的从JobsChannel内部任务队列中拿任务
	for {
		select {
		case task := <-p.JobsChannel:
			task.Execute()
			fmt.Println("worker ID ", work_ID, " 执行完毕任务")
		default:
            break
		}
	}

}

//让协程池Pool开始工作
func (p *Pool) Run() {
	//1,首先根据协程池的worker数量限定,开启固定数量的Worker,
	//  每一个Worker用一个Goroutine承载
	for i := 0; i < p.worker_num; i++ {
		go p.worker(i)
	}

	//2, 从EntryChannel协程池入口取外界传递过来的任务
	//   并且将任务送进JobsChannel中

	for{
		select {
		case task := <-p.EntryChannel:
			p.JobsChannel <- task
		default:
			break
		}

	}

	//3, 执行完毕需要关闭JobsChannel
	close(p.JobsChannel)

	//4, 执行完毕需要关闭EntryChannel
	close(p.EntryChannel)
}
```

`3`select多路复用

```go
package main

import "fmt"

func fibna(c,quit chan int)  {
	x,y := 1,1
	for{
		select {
			//c读取数据前触发
			case c<-x:
				y = x
				x = x + y
			//向quit中写数据触发
			case <-quit:
				fmt.Println("quit")
				return
		}
	}
}
func main(){
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i :=0;i<5;i++{
			fmt.Println(<-c)
		}
		quit <-0
	}()

	fibna(c,quit)

}
/**
1
2
4
8
16
quit

 */
```



`4`互斥锁

```go
package main

import (
	"fmt"
	"sync"
)
var x int64
var wg sync.WaitGroup
var lock sync.Mutex

func main() {

	wg.Add(3)
	go add()
	go add()
	go add()
	wg.Wait()
	fmt.Println(x)

}


func add() {
	for i := 0; i < 10; i++ {
		lock.Lock() // 加锁
		x = x + 1
		lock.Unlock() // 解锁
	}
	wg.Done()
}
```

`5`读写锁

```go
package main

import (
	"fmt"
	"sync"
	"time"
)
var (
	wg     sync.WaitGroup
	rwlock sync.RWMutex
)

func write() {
	rwlock.Lock() // 加写锁
	fmt.Println("正在写")
	time.Sleep(2*time.Second) // 假设读操作耗时10毫秒
	rwlock.Unlock()                   // 解写锁
	wg.Done()
}

func read() {
	rwlock.RLock()               // 加读锁
	fmt.Println("正在读")
	time.Sleep(time.Second) // 假设读操作耗时1毫秒
	rwlock.RUnlock()             // 解读锁
	wg.Done()
}

func main() {

	start := time.Now()
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go write()
	}

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go read()
	}

	wg.Wait()
	end := time.Now()
	fmt.Println(end.Sub(start))

}

```

`6`原子操作

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
	"time"
)
var x int64
var l sync.Mutex
var wg sync.WaitGroup

// 原子操作版加函数
func atomicAdd() {
	atomic.AddInt64(&x, 1)
	wg.Done()
}

func main() {

	start := time.Now()
	for i := 0; i < 10000; i++ {
		wg.Add(1)
		go atomicAdd() // 原子操作版add函数 是并发安全，性能优于加锁版
	}
	wg.Wait()
	end := time.Now()
	fmt.Println(x)
	fmt.Println(end.Sub(start))

}

```

---

**json操作**

`1`结构体转json

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	Id int `json:"id"`
	Name string `json:"name"`
	Age int `json:"age"`
}


func main(){
	user := User{
		1,
		"zhang",
		20,
	}
	jsonStr,err := json.Marshal(user)
	if err != nil{
		fmt.Println("json marshal error",err)
		return
	}
	fmt.Printf("%s",jsonStr)
	//{"id":1,"name":"zhang","age":20}
}

```

`2`json转结构体

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	Id int `json:"id"`
	Name string `json:"name"`
	Age int `json:"age"`
}


func main(){
	user := User{}
	str := "{\"id\":1,\"name\":\"zhang\",\"age\":20}"
	err := json.Unmarshal([]byte(str),&user)

	if err != nil{
		fmt.Println("json marshal error",err)
		return
	}
	fmt.Println(user)
}
```

---

**随机数**

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {

	rand.Seed(time.Now().Unix())
	fmt.Println(rand.Intn(100))

}

```



new()和make()的区别

- new(T)  回一个指向类型为 T，值为 0 的地址的指针，它适用于值类型如数组和结构体 

- make(T) 返回一个类型为 T 的初始值，它只适用于3种内建的引用类型:切片、map 和 channel

---

**网络编程**

```go
package main

import (
	"fmt"
	"net/http"
)

// handler函数
func myHandler(w http.ResponseWriter, r *http.Request) {
	fmt.Println(r.RemoteAddr, "连接成功")
	// 请求方式：GET POST DELETE PUT UPDATE
	fmt.Println("method:", r.Method)
	// /go
	fmt.Println("url:", r.URL.Path)
	fmt.Println("header:", r.Header)
	fmt.Println("body:", r.Body)
	// 回复
	w.Write([]byte("www.5lmh.com"))
}

func main() {

	http.HandleFunc("/go", myHandler)
	// addr：监听的地址
	// handler：回调函数
	http.ListenAndServe("127.0.0.1:8000", nil)

}

```

---

**读取文件**

```go
package main

import (
	"os"
)

func openfile(){
	buf := make([]byte, 1024)
	f, _ := os.Open("C:\\Users\\zhanghuan\\Desktop\\1.txt")
	defer f.Close()
	for {
		n, _ := f.Read(buf)
		if n == 0 {
			break

		}
		os.Stdout.Write(buf[:n])
	}
}
func main(){
	openfile()
}

```



`1`基本操作

1）创建

```go
package main

import (
	"log"
	"os"
)


func checkErr(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
func main(){
	var newFile,err=os.Create("C:\\Users\\zhanghuan\\Desktop\\text.txt")
	checkErr(err)
	log.Println(newFile)
	newFile.Close()

}

```



2）重命名和移动

```go
package main

import (
	"log"
	"os"
)

func checkErr(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
func main(){
	var err=os.Rename("C:\\Users\\zhanghuan\\Desktop\\text.txt","C:\\Users\\zhanghuan\\Desktop\\text1.txt")
	checkErr(err)
}

```



3）文件信息

```go
package main

import (
	"fmt"
	"log"
	"os"
)

func checkErr(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
func main(){
	var fileInfo, err = os.Stat("C:\\Users\\zhanghuan\\Desktop\\text1.txt")
	fmt.Printf("%v\n",fileInfo)
	//&{text1.txt 32 {857397888 30901355} {857397888 30901355} {857397888 30901355} 0 0 0 0 {0 0} C:\Users\zhanghuan\Desktop\text1.txt 0 0 0 false}
	checkErr(err)
}
```

如果文件不存在会打印出错误

4）删除文件

```go
package main

import (
	"log"
	"os"
)

func checkErr(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
func main(){
	var  err = os.Remove("C:\\Users\\zhanghuan\\Desktop\\text1.txt")
	checkErr(err)
}

```



`2`检查读写权限

```go
package main

import (
	"fmt"
	"log"
	"os"
)

func checkErr(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
func main(){
	var  info,err = os.OpenFile("C:\\Users\\zhanghuan\\Desktop\\text11.txt",os.O_WRONLY,0666)
	if err!=nil{
		if os.IsPermission(err){
			fmt.Println("没有权限")
		}
		if os.IsNotExist(err){
			fmt.Println("文件不存在")
		}
	}
	println(info)
	checkErr(err)
}

```



`3`改变权限、拥有者、时间戳

```go
package main

import (
	"fmt"
	"log"
	"os"
	"time"
)

func checkErr(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
func main(){
	//授权
	err := os.Chmod("C:\\Users\\zhanghuan\\Desktop\\text1.txt",0777)
	checkErr(err)

	//改变拥有者，仅支持linux
	err = os.Chown("C:\\Users\\zhanghuan\\Desktop\\text1.txt",os.Geteuid(),os.Getegid())
	//checkErr(err)

	//修改文件日期
	twoDaysFromNow:=time.Now().Add(48*time.Hour)
	err = os.Chtimes("C:\\Users\\zhanghuan\\Desktop\\text1.txt",twoDaysFromNow,twoDaysFromNow)
	checkErr(err)

	a, _ := os.Stat("C:\\Users\\zhanghuan\\Desktop\\text1.txt")
	fmt.Println(a.ModTime())
	//2021-07-31 20:02:05.3058743 +0800 CST
}

```



`4`创建链接

```go
package main

import (
	"log"
	"os"
)

func main() {
	// 创建硬链接
	err := os.Link("C:\\Users\\zhanghuan\\Desktop\\text1.txt", "C:\\Users\\zhanghuan\\Desktop\\texta.txt")
	if err != nil {
		log.Fatal(err)
	}

	// 创建软链接
	err = os.Symlink("C:\\Users\\zhanghuan\\Desktop\\text1.txt", "C:\\Users\\zhanghuan\\Desktop\\textb.txt")
	if err != nil {
		log.Fatal(err)
	}
}
```



`5`文件复制

```go
package main

import (
	"io"
	"log"
	"os"
)

func main() {
	file,err := os.Open("C:\\Users\\zhanghuan\\Desktop\\text1.txt")
	checkerror(err)
	distfile ,err:= os.Create("C:\\Users\\zhanghuan\\Desktop\\text2.txt")
	checkerror(err)
	io.Copy(distfile,file)
}
func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

`6`写入文件

1）普通方式

```go
package main

import (
	"fmt"
	"log"
	"os"
)

func main() {
	file,err := os.OpenFile("C:\\Users\\zhanghuan\\Desktop\\text1.txt",os.O_WRONLY|os.O_CREATE|os.O_APPEND,0666)
	checkerror(err)
	content := []byte("Bytes!\n")
	bytesWritten, err := file.Write(content)
	checkerror(err)
	fmt.Println(bytesWritten)
}
func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

2）快速写入

```go
package main

import (
	"io/ioutil"
	"log"
)

func main() {
    //覆盖
	err := ioutil.WriteFile("C:\\Users\\zhanghuan\\Desktop\\text1.txt",[]byte("Bytes!\n"),0666)
	checkerror(err)
}
func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

3）缓存写

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {
	file,err:=os.OpenFile("C:\\Users\\zhanghuan\\Desktop\\text1.txt",os.O_WRONLY,0666)
	checkerror(err)
	defer file.Close()
	bufferdWriter:=bufio.NewWriter(file)
	result,err:=bufferdWriter.Write([]byte{65,66,67})
	checkerror(err)
	bufferdWriter.Flush()
	fmt.Println(result)

}
func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```



`7`压缩和解压

1）zip包

```go
package main

import (
	"archive/zip"
	"fmt"
	"log"
	"os"
)
//压缩
func main() {
	file,err:=os.Create("C:\\Users\\zhanghuan\\Desktop\\1.zip")
	checkerror(err)
	defer file.Close()
	zipwriter := zip.NewWriter(file)

	for i:=0;i<10;i++{
		//int转换成string
		str := fmt.Sprintf("%d", i)
		//zip包内放入文件
		filewriter,err := zipwriter.Create(str)
		//zip包放入的文件写入内容
		filewriter.Write([]byte("aaaa"))
		checkerror(err)
	}

	zipwriter.Close()

}
func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

```go
package main

import (
	"archive/zip"
	"io"
	"log"
	"os"
	"path/filepath"
)

func main() {

	zipreader,err := zip.OpenReader("C:\\Users\\zhanghuan\\Desktop\\1.zip")
	checkerror(err)
	defer zipreader.Close()

	for _,file := range zipreader.Reader.File{//遍历zip中的文件
		//打开当前文件
		zippedfile,err := file.Open()

		checkerror(err)
		//定义目标文件夹
		path := filepath.Join("C:\\Users\\zhanghuan\\Desktop\\zip",file.Name)

		if file.FileInfo().IsDir(){
			os.MkdirAll("C:\\Users\\zhanghuan\\Desktop\\zip",file.Mode())
		}else{
			outputfile,err :=os.OpenFile(path,os.O_WRONLY|os.O_CREATE|os.O_TRUNC,file.Mode())
			checkerror(err)
			//将文件内容复制到目标文件
			_, err = io.Copy(outputfile, zippedfile)
			checkerror(err)
			outputfile.Close()
		}
	}

}

func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

2）gzip包

```go
package main

import (
	"compress/gzip"
	"log"
	"os"
)

func main() {

	outputfile,err := os.Create("C:\\Users\\zhanghuan\\Desktop\\2.gz")
	checkerror(err)
	defer outputfile.Close()
	gzipwriter := gzip.NewWriter(outputfile)

	_,err = gzipwriter.Write([]byte("Gophers rule!\n"))
	checkerror(err)
}

func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

```go
package main

import (
	"compress/gzip"
	"io"
	"log"
	"os"
)

func main() {

	reader,err := os.Open("C:\\Users\\zhanghuan\\Desktop\\2.gz")
	checkerror(err)

	gzipreader,err := gzip.NewReader(reader)
	defer gzipreader.Close()

	dist,err := os.Create("C:\\Users\\zhanghuan\\Desktop\\2")
	io.Copy(dist,gzipreader)
	dist.Close()
}

func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

`8`临时文件

```go
package main

import (
	"io/ioutil"
	"log"
	"os"
)

func main() {

	tempDirPath, err := ioutil.TempDir("C:\\Users\\zhanghuan\\Desktop\\","t")
	checkerror(err)
	tempFile, err := ioutil.TempFile(tempDirPath, "myTempFile.txt")
	checkerror(err)
	err = tempFile.Close()

	//删除临时文件
	err = os.Remove(tempFile.Name())
	if err != nil {
		log.Fatal(err)
	}
	//删除临时文件目录
	err = os.Remove(tempDirPath)
	if err != nil {
		log.Fatal(err)
	}


}

func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

`9`哈希和摘要

```go
package main

import (
	"crypto/md5"
	"crypto/sha1"
	"crypto/sha256"
	"crypto/sha512"
	"fmt"
	"io/ioutil"
	"log"
)

func main() {

	data,err := ioutil.ReadFile("C:\\Users\\zhanghuan\\Desktop\\text1.txt")
	checkerror(err)

	fmt.Printf("Md5: %x\n\n", md5.Sum(data))
	fmt.Printf("Sha1: %x\n\n", sha1.Sum(data))
	fmt.Printf("Sha256: %x\n\n", sha256.Sum256(data))
	fmt.Printf("Sha512: %x\n\n", sha512.Sum512(data))
	
}

func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```



`10`文件下载

```go
package main

import (
	"io"
	"log"
	"net/http"
	"os"
)

func main() {

	newFile, err := os.Create("C:\\Users\\zhanghuan\\Desktop\\devdungeon.html")
	checkerror(err)
	defer newFile.Close()

	url := "http://baidu.com"
    response,err := http.Get(url)
    defer response.Body.Close()

    length,err := io.Copy(newFile,response.Body)
	checkerror(err)
    log.Print(length)
}

func checkerror(err error){
	if err!=nil{
		log.Fatal(err)
	}
}
```

---

**beego**

安装bee工具

```
git config --global http.sslVerify false
go get -u github.com/beego/bee
```

github加速

```
C:\Windows\System32\drivers\etc\hosts
```

这个文件内新增

```
140.82.112.4 github.com
199.232.5.194 github.global.ssl.fastly.net
```


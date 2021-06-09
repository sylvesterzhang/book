---
layout: post
title: Typescript基础知识
tags: typescript
categories: frontend
---

typescript安装、编译、数据类型、函数声明、ES5中的类、TS中的类、接口、泛型、装饰器

**Typescript安装**

```
npm installl -g typescript
```

---

**编译成js**

```
tsc aaa.ts
```

执行该命令后会自动生成js文件

---

**在VScode中设置保存后自动编译**

1.生成tscconfig.json文件

```
tsc --init
```

2.修改tscconfig.json文件中outDir:'/js'，此后生成的js文件会存入js的文件夹中

3.VScode编辑器里

任务-运行任务，点击监视tsconfig.json

---

**数据类型**

`1`.布尔类型	boolean

`2`.数字类型	number

`3`.字符串类型	string

`4`.数组类型	array

```typescript
let ar:number[]=[1,2,3]
```

`5`.元组类型	tuple

```typescript
let ar:[string,number,number]=["1",2,3]
```

*实际上就是一个允许不同数据类型的数组*

`6`.枚举类型	enum

```typescript
enum color {red,green,blue}
let c:color=color.green
console.log(c)//1
```

`7`.任意类型	any

```typescript
window.onload=function(){
    let box:any=document.getElementById("div")//获取元素时，元素对象类型为any
    box.innerHTML="hello"
}
```

`8`.null和undefined

```typescript
var num:number//num未进行初始化，编译报错
console.log(num)

var num:number|null|undefined//num如果未初始化，赋值为null或undefined
console.log(num)
```

`9`.void类型

```typescript
function run():void{//该方法无返回值
	console.log("run")
}
```

`10`.never类型

*哪些永远不存在的值的类型，返回never的函数必须存在无法达到的终点*

```typescript
//这是官方文档的例子
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");//会抛出错误
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```

---

**函数声明**

`1`.普通方式和匿名函数方式声明

```typescript
//普通方式
function run():void{
	console.log("run")
}
run()//输出run

//匿名函数方式
var run1=function():void{
	console.log("run")
}
run1()//输出run
```

`2`.参数、可选参数、默认参数

```typescript
//参数
function getinfo(name:string,age:number):string{
    return `${name}--${age}`
}
console.log(getinfo("aaa",25))//输出aaa--25

//可选参数，必须配置到参数的最后面
function getinfo1(name:string,age?:number):string{
    return `${name}--${age}`
}
console.log(getinfo1("aaa"))//输出aaa--undefined

//默认参数
function getinfo2(name:string,age:number=20):string{
    return `${name}--${age}`
}
console.log(getinfo2("aaa"))//输出aaa--20
```

*可选参数这里和js完全不同，如果时可选参数必须用“?”标记出来，否则会报错。而在js中不传第二个参数时不会报错，第二个参数会默认为undefined。*

`3`.剩余参数，“...”接收传过来的所有参数

```typescript
function sum(...result:number[]):number{
    var sum=0
    for(var i=0;i<result.length;i++){
        sum+=result[i]
    }
    return sum
}
console.log(sum(1,2,3,4))//输出 10
```

`4`.解构用于函数声明

```typescript
type C = { a: string, b?: number }
function f({ a, b }: C): void {
    console.log("a:",a,"b:",b)
}
f({a:"qwe"})//输出 a: qwe b: undefined
```

`5`.方法重载

```typescript
function getInfo(name:string):string
function getInfo(name:string,age:number):string

function getInfo(name:string,age?:any):any{
    if(age){
        return "我叫："+name+"我的年龄是："+age
    }else{
        return "我叫："+name
    }
}
console.log(getInfo("zhang"))//输出 我叫：zhang
console.log(getInfo("zhang",18))//输出  我叫：zhang我的年龄是：18
//console.log(getInfo(123)) 这样写是错误的
```

---

**ES5中类**

`1`.定义

```javascript
function person(name,age){
            this.name=name
            this.age=age
            this.run=function(){
                console.log(this.name+'在运动')
            }
        }
        person.getinfo=function(){
            console.log("我是静态方法")
        }//定义静态方法

        //原型链上定义属性、方法
            
        person.prototype.sex="男"//原型链上属性会被多个实例共享
        person.prototype.work=function(){
            console.log(this.name+"在工作")
        }

        person.getinfo()//执行静态方法，输出	我是静态方法

        p=new person("zhang",18)
        p.run()//执行对象方法，输出	zhang在运动
        p.work()//执行原型链方法，输出	zhang在工作
```

`2`.继承

1）对象冒充实现继承

```javascript
function web(name,age){
	person.call(this,name,age)//对象冒充实现继承
}
var w=new web("zhang",18)
w.run()//输出	zhang在运动
w.work()//报错，对象不支持work属性或方法
```

*只可继承构造函数里的属性和方法，无法继承原型链里的属性和方法*

2）原型链实现继承

```javascript
function web(name,age){
            
}
web.prototype=new person("zhang",18)
var w=new web()
w.run()//输出	zhang在运动
w.work()//输出	zhang在工作
```

*既可继承构造函数里的属性和方法，又可以继承原型链里的属性和方法。问题也很明显，实例化时子类无法给父类传参*

3）对象冒充组合原型链实现继承

```javascript
function web(name,age){
	person.call(this,name,age)
}
web.prototype=person.prototype
var w=new web("zhang",18)
w.run()//输出	zhang在运动
w.work()//输出	zhang在工作
```

*既可继承构造函数里的属性和方法，又可以继承原型链里的属性和方法，解决原型链继承中的问题*

---

**TS中的类**

`1`类的定义

```typescript
class person{
    name:string
    constructor(name:string){
        this.name=name
    }
    run():void{
        console.log(`${this.name}在跑步`)
    }
}

var p=new person('zhang')
p.run()//输出	zhang在跑步
```

`2`类的继承

```typescript
class worker extends person{
    constructor(name:string){
        super(name)
    }
    work():void{
        console.log(`${this.name}在工作`)
    }
}

var w=new worker('huan')
w.run()//输出	huan在跑步
w.work()//输出	huan在工作
```

`3`类的修饰符

public 公有，在类、子类里外都可访问

protected 保护型，在类、子类里可访问，在类外不可访问

private 私有型，在类里可访问，字类里、外都不可访问

`4`抽象类

```typescript
abstract class animal{
    public name:string
    constructor(name:string){
        this.name=name
    }
    abstract eat():any//定义抽象方法，其字类必须实现该方法
}

class dog extends animal{
    constructor(name:string){
        super(name)
    }
    eat():any{
        console.log(`${this.name}在吃狗粮`)
    }
}

var d=new dog('小黑')
d.eat()//输出	小黑在吃狗粮
```

---

**接口**

为达到规范，定义的一种约束

`1`.约束

```typescript
//对函数参数的约束
interface config{
    type:string;
    url:string;
    data?:string;
    dataType:string;
}
function ajax(config:config){
    var xhr=new XMLHttpRequest()
    xhr.open(config.type,config.url,true)
    xhr.send(config.data)
    xhr.onreadystatechange=function(){
        if(xhr.readyState==4&&xhr.status==200){
            console.log("success")
            if(config.dataType=='json'){
                console.log(JSON.parse(xhr.responseText))
            }else{
                console.log(xhr.responseText)
            }
        }
    }
}

ajax({
    type:'get',
    data:'name=zhang',
    url:'http://a.itying.com/api/productlist',
    dataType:'json'
})

//对函数的约束
interface encrypt{
    (key:string,value:string):string
}

var md5:encrypt=function(key:string,value:string):string{
    return key+value
}

console.log(md5('zhang','huan'))

//对数组的约束
interface userarr{
    [index:number]:string
}
var arr:userarr=['aaa','bbb']
console.log(arr[0])

//对对象的约束
interface userobj{
    [index:string]:string
}
var obj:userobj={
    name:'zhang'
}
console.log(obj.name)

//对类的约束
interface animal{
    name:string
    eat(str:string):void
}
class dog implements animal{
    name:string
    constructor(name:string){
        this.name=name
    }
    eat(){
        console.log(`${this.name}吃狗粮`)
    }
}
var xiaohei=new dog("小黑")
xiaohei.eat()

class cat implements animal{
    name:string
    constructor(name:string){
        this.name=name
    }
    eat(food:string){
        console.log(`${this.name}吃${food}`)
    }
}
var xiaohua=new cat("小花")
xiaohua.eat("耗子")
```

`2`继承

```typescript
//官方文档的多继承例子
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};//实例化一个对象
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

---

**泛型**

在定义函数、接口或类的时候，不预先设定其类型，而在调用时在指定类型

```typescript
function go<T>(arg:T):T{
    console.log(arg)
    return arg
}
go('go,go,go……')//输出	gp,go,go……
```

---

**装饰器**

`1`修改tsconfig.json文件，使得VS支持装饰器功能

```
"experimentalDecorators": true,
```

`2`使用

```typescript
//类装饰器
function logclass(params:any) {//params就是当前类
    params.prototype.apiurl="动态扩展属性"//给类扩展属性
    params.prototype.run=function(){//给类扩展方法
        console.log("这是扩展的run方法")
    }
}

@logclass
class httpclient{

}

var h:any=new httpclient()
console.log(h.apiurl)//输出	动态扩展属性
h.run()//输出	这是扩展的run方法

//装饰器工厂
function logclass(params:any) {//外部参数
    return function(target:any){//target是当前类
        console.log(params)
        console.log(target)
    }
}

@logclass("hello")//可传参
class httpclient{

}

var h=new httpclient()
//输出  hello
//输出  f httpclient() {
//    }

//装饰器修改类的构造函数
function logclass(params:any):any {
    return class extends params{
        constructor(){
            super(name)
            this.name="修改后的构造函数"
            console.log(this.name)
        }
    }
}

@logclass//可传参
class httpclient{
    name:string|undefined
    constructor(){
        this.name="修改前的构造函数"
        console.log(this.name)
    }
}

var h=new httpclient()
//输出  修改前的构造函数
//输出  修改后的构造函数

//属性装饰器
function logproperty(params:any){//params外部参数
    return function(target:any,attr:any){//target是原型对象，attr是对象的属性，这里会将修饰的属性自动传入
        console.log(target)
        console.log(attr)
        target[attr]=params
    }
}

class httpclient{
    @logproperty("xxx.com")
    name:string|undefined
    constructor(){
    }
}

var h=new httpclient()
console.log(h.name)
//输出  {constructor: ƒ}
//输出  name
//输出  xxx.com

//方法装饰器
//传入3个参数分别接收：类的原型对象、成员名字、成员属性描述符
function get(params:any):any {
    return function(target:any,methodname:any,desc:any){
        console.log(target)
        console.log(methodname)
        console.log(desc)
    }
}


class httpclient{
    name:string|undefined
    constructor(){
        this.name="zhanghuan.top"
        console.log(this.name)
    }
    @get("abcdefg")//放在方法上
    getdata(){
        console.log(this.name)
    }
}

var h=new httpclient()
//输出  {getdata: ƒ, constructor: ƒ}
//输出  getdata
//输出  {value: ƒ, writable: true, enumerable: true, configurable: true}
//输出  zhanghuan.top
```

`3`装饰器执行顺序

属性>方法>方法参数>类


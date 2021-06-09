---
layout: post
title: js新特性第一部分
tags: javascript
categories: frontend
---

var、let、const的区别，对象被定义为常量不受保护，自执行函数注意事项，箭头函数特点，对象扩展运算和对象解构运算,label语句，for...in...和for...of...的区别

**var、let、const的区别**

|                  | var  | let      | const  |
| ---------------- | ---- | -------- | ------ |
| 作用域           | 函数 | 花括号内 | 全局   |
| 是否可重复声明   | 可以 | 不可以   | 不可以 |
| 声明后是否可修改 | 是   | 是       | 否     |
| 是否属于window   | 是   | 否       | 否     |

---

**对象被定义为常量不受保护**

```javascript
const a={"key":"value"}
a.key="abc"
const b=["a","b","c"]
b.push
```

该代码执行不会报错

---

**七种基本数据类型**

布尔值、null、undefined、数字、任意精度整数、字符串、代表

---

**自执行函数注意事项**

```javascript
var name="zhang";//必须要加这个分号，不然后面自执行函数报错
(function(){
	console.log(name)
})()
```

---

**箭头函数特点**

没有原型属性，没有this，不可用作构造函数，所以箭头函数继承封闭范围内的this值

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <button>buttons</button>
</body>
<script>
    window.onload = function() {
        let userName = "zhang";
        var btn = document.getElementsByTagName("button")[0];
        btn.onclick = function() {
            (function() {
                console.log(this)//window
            })();
            //该函数有自己的this，指向window
            
            (() => {
                console.log(this)//button
            })()
            //该函数无自己的this，指向上一层对象
        }
    }
</script>

</html>
```

---



**this的区分**

```javascript
let username="zhang"
let user={
	username:"huan",
	say(){
		console.log(username)//zhang
		console.log(this.username)//huan
		setTimeout(function(){
			console.log(this.username)//undefined
		},1000)
		setTimeout(()=>{
			console.log(this.username)//huan
		},2000)
	}
	user.say()
}
```

---

**对象扩展运算和对象解构运算**

 扩展：在不改变原对象的前提下，生成一个新对象继承原对象，并新增属性

解构：在不改变原对象的前提下，将原对象的属性复制出来

```javascript
window.onload = function() {
        var obj = {
            a: 00,
            b: 123
        }
        var ext = {
            ...obj,
            c: 20
        }
        console.log(ext)//Object{a:0,b:123,c:20}
		//扩展
    
        var props = {
            "color": "red",
            "lineheight": 1.5,
            "fontsize": "12px"
        }
        var {
            color,
            lineheight
        } = props
        console.log(color, lineheight)//red 1.5
    	//解构

    }
```

---

**label语句**

对循环进行标记，便于指定`break`或`continue`的循环对象

```javascript
//停止外层循环
window.onload = function() {
        var num = 0
        outter:
        for (var i = 0; i < 10; i++) {
                inner: 
            	for (var j = 0; j < 10; j++) {

                    if (i == 5 && j == 5) {
                        break outter;
                    }
                    num++;
                }
            }
        console.log(num)//55

    }
```

```javascript
//停止内层循环
window.onload = function() {
        var num = 0
        outter:
        for (var i = 0; i < 10; i++) {
                inner:
            	for (var j = 0; j < 10; j++) {

                    if (i == 5 && j == 5) {
                        break inner;
                    }
                    num++;
                }
            }
        console.log(num)//95

    }
```



```javascript
//不使用label标记，则停止内层循环
window.onload = function() {
        var num = 0
        for (var i = 0; i < 10; i++) {

            	for (var j = 0; j < 10; j++) {

                    if (i == 5 && j == 5) {
                        break;
                    }
                    num++;
                }
                
            }
        console.log(num)//95

    }
```

---

**for...in...和for...of...的区别**

`1`前者适用范围更广；后者仅适用`array`、`map`、`set`等的可迭代，不适用`object`

`2`前者既可以迭代对象的key，又能迭代对象的value；后者只可迭代出对象的value

```javascript
window.onload = function() {
    	//for...in...
        let obj = {
            "firstname": "huan",
            "lastname": "zhang"
        }
        for (var item in obj) {
            console.log(item,obj[item])//firstname huan,lastname zhang
        }
    
        let ar = ["a", "b", "c"]
        for (var item in ar) {
            console.log(item, ar[item])//0 a,1 b,2 c
        }

    }
```



```javascript
window.onload = function() {
    	//for...of...
    	let ar = ["a", "b", "c"]
        for (var item of ar) {
            console.log(item)//a,b,c
        }
        let obj = {
            "firstname": "huan",
            "lastname": "zhang"
        }
        for (var item of obj) {
            console.log(item)//此处报错，TypeError:obj is not iterable
        }
        

    }
```

---

**数组的some方法**

遍历数组中每个元素，判断其****是否满足指定函数的指定条件****,返回true或者false

```javascript
let arr=[1,2,20,13,22]
arr.list.some((item,index,array)=>{
	//item 当前元素
	//index 当前元素下标
	//array 当前数组
	//1.如果一个元素满足条件,返回true,且后面的元素不再被检测
	//2.所有元素都不满足条件，则返回false
	//3.不会改变原始数组
	//4.不会对空数组进行检测;数组为空的话，直接返回false
	if(item>18){
		return true
	}
})
```

---

**数组的filter方法**

遍历数组中每个元素，更加其内部条件，返回一个新数组

```javascript
let arr=[1,5,51,6,8]
let new_arr=arr.filter(item=>{
	if(item>2){
		return item
	}
})
console.log(new_arr)//(4) [5, 51, 6, 8]
```


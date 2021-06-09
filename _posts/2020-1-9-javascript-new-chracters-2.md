---
layout: post
title: js新特性第二部分
tags: javascript
categories: frontend
---

实现map函数、嵌套函数和闭包、arguments对象、函数参数、关系操作符、遍历数组foreach方法、map对象与object对象的区别、promise对象、生成器

**实现Map函数**

传入处理函数和数组，返回值为将数组内的数按照函数规则处理后新生成的数组

```javascript
window.onload = function() {
        function map(f, ar) {
            var res = [],
                i
            for (i = 0; i < ar.length; i++) {
                res.push(f(ar[i]))
            }
            return res
        }
        //定义map函数
    
        ar = [1, 2, 3]
        function f(a) {
            return a * a
        }
        result = map(f, ar)
        console.log(result)//Array[3] [1,4,9]

    }
```

---

**嵌套函数和闭包**

嵌套函数是指在一个函数内部，再定义另一个函数。闭包是指拥有独立环境与变量的表达式（函数）。

特点：

`1`内部函数只能通过外部函数层级调用

`2`内部函数可以访问外部函数的参数和变量，但外部函数不可访问内部函数的参数和变量

---

**arguments对象**

函数的实参会保存在arguments对象中

```javascript
window.onload=function(){
    function go(a,b,c){
        console.log(arguments)
        //Arguments { 0: "first", 1: "second", 2: "third", … }
        
        console.log(arguments.length)//3
        for(item in arguments){
            console.log(arguments[item])
        }
        //first
        //second
        //third
    }
    go("first","second","third")
}
```

---

**函数参数**

`1`函数参数的默认值是`undifined`

`2`不确定传入的参数个数，参数可以用...args表示，在函数中将会得到一个名为args的数组

---

**关系操作符**

in判断某个对象是否存在这个属性

instanceof判断某个对象是否属于某个类型

---

**遍历数组的foreach方法**

数组自带一个foreach方法，传入一个处理函数便能实现对数组的遍历

```javascript
var colors = ['red', 'green', 'blue'];
colors.forEach(function(color) {
  console.log(color);
});
//red
//green
//blue
```

---

**Map对象和Object对象的区别**

| Map对象                    | Object对象             |
| -------------------------- | ---------------------- |
| 任意值                     | 键只能是字符串或Symbol |
| size属性直接获取键值对个数 | 无法直接获取键值对个数 |
| 可直接迭代                 | 无法直接迭代           |

---

**Promise对象**

三种状态：Pending，Resolved，Rejected

```javascript
window.onload=function(){
    
    const p = function(){
        let num = Math.random();
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                num > 0.5 ? resolve(num) : reject(num);
                }, 1000);
            })
    };
    p().then( (data)=>{console.log("success",data)},(error)=>{console.log("failure",error)})
    //success 0.7462217733600771
    //failure 0.003417836372593297

}
```

---

**生成器**

```javascript
function* makeRangeIterator(start = 0, end = Infinity, step = 1) {
        for (let i = start; i < end; i += step) {
            yield i;
        }
    }
    var a = makeRangeIterator(1,10,2)
    console.log(a.next())
    console.log(a.next())
    console.log(a.next())
```

---

**模块暴露**

```javascript
//定义一个模块abc.js，暴露变量供外部使用
let info={
	name:"zhanghuan"
}
export default info
//一个模块中只可有一个default

export var title="过眼奇峰云已夏，关心暑雨麦方秋。"
export var title2="有志竟能成耿弇，诸艰偏欲试张纲。"
//一个模块中可有多个var
```

```javascript
import info1, { title1 } from './abc.js'; //导入default暴露的变量，外部可以用任意变量名接收
console.log(info1.name)//zhanghuan

import {title,title2} from './abc.js'
console.log(title,title2)//过眼奇峰云已夏，关心暑雨麦方秋。 有志竟能成耿弇，诸艰偏欲试张纲。
```


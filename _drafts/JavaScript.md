BOM浏览器对象模型

`1`对象分类

| 对象名    | 解释           |
| --------- | -------------- |
| window    | 窗口对象       |
| navigator | 浏览器对象     |
| screen    | 显示器屏幕对象 |
| hsitory   | 历史记录对象   |
| location  | 地址位置对象   |

`2`window对象

```
//显示一段消息和确认按钮的警告框
alert();

//显示带有一段消息，确认、取消按钮的对话框，点击确认改方法的返回值是true，取消则是false
confirm();

//显示让用户输入字符串的对话框，返回用户输入的字符串
prompt();

//打开新的窗口，传递的参数是要跳转的地址字符串，返回新的window对象
open();

//关闭当前窗口
close();

//一次性定时器
setTimeout();

//循环定时器
setInterval();
```

`3`location对象

```
//刷新页面
reload();

//地址属性，可获取、重新赋值。如果重新赋值后，会跳转到新地址
href
```

`4`location对象

```
//加载history列表中的前一个url
back();

//加载history列表
forward();

//加载history列表中的某个页面，参数为数字，正数表示前进多少个页面，负数表示后退多少个页面
go();

//返回当前窗口历史列表中的url个数
length
```


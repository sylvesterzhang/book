---
layout: post
title: 正则表达式总结
tags: 正则表达式
categories: backend
---

元字符、反义、转义、贪婪与懒惰、分组、后向引用、零宽断言、Python中的re模块

参考文档

[https://deerchao.cn/tutorials/regex/regex.htm](https://deerchao.cn/tutorials/regex/regex.htm)

### 元字符

| 元字符 | 说明                           |
| ------ | ------------------------------ |
| .      | 匹配除换行符以外的其他任意字符 |
| \w     | 匹配字母、数字、下划线、汉字   |
| \s     | 匹配任意空白字符               |
| \d     | 匹配任意数字                   |
| \b     | 匹配单词的开始或结束           |
| ^      | 匹配字符串的开始               |
| $      | 匹配字符串的结束               |

### 反义

| 语法     | 说明                                       |
| -------- | ------------------------------------------ |
| \W       | 匹配任意不是字母、数组、下划线、汉字的字符 |
| \S       | 匹配任意不是空白符的字符                   |
| \D       | 匹配任意非数字的字符                       |
| \B       | 匹配不是单词开头或结尾的位置               |
| [^x]     | 匹配除x以外的任意字符                      |
| [^aeiou] | 匹配除aeiou以外的任意字符                  |

### 转义

想要匹配 . ? *这样的字符，需要进行转义，使用\ ，如 \\.  \\?  \\\* 

重复模式

| 语法  | 说明             |
| ----- | ---------------- |
| *     | 重复0次或更多次  |
| +     | 重复一次或更多次 |
| ?     | 重复零次或一次   |
| {n}   | 重复n次          |
| {n,}  | 重复n次或更多次  |
| {n,m} | 重复n次到m次     |

### 贪婪与懒惰

| 语法   | 说明                              |
| ------ | --------------------------------- |
| *?     | 重复任意次，但尽可能少的重复      |
| +?     | 重复1次或更多次，但尽可能少的重复 |
| ??     | 重复0次或1次，但尽可能少的重复    |
| {n,m}? | 重复n到m次，但尽可能少的重复      |
| {n,}?  | 重复n次以上，但尽可能少的重复     |

### 分组

使用()来指定要重复的子表达式，然后对这个表达式进行重复，如 (abc)?表示0个或1个abc。分组可分为捕获分组、不捕获分组、命名分组。

1）捕获分组

表达式`(A)(B(C))` 中，存在四个这样的组：

| **0** | (A)(B(C)) |
| ----- | --------- |
| **1** | (A)       |
| **2** | (B(C))    |
| **3** | (C)       |

组`0`始终代表整个表达式

2）不捕获分组

以 (?) 开头的组是纯的非捕获组，(?:Pattern)，它的作用就是匹配Pattern字符，好处就是不捕获文本，不将匹配到的字符存储到内存中，从而节省内存

例: 匹配indestry或者indestries

我们可以使用indestr(y|ies)或者indestr(?:y|ies)

例: (?:a|A)123(?:b)可以匹配a123b或者A123b

3）命名分组

语法: (?<name>)

js中

```javascript
"http://www.baidu.com".match(/(?<protocol>\w+):\/\/(?<domain>\w+\.\w+\.\w+)/);

// 返回结果
0: "http://www.baidu.com"
1: "http"
2: "www.baidu.com"
groups:
    domain: "www.baidu.com"
    protocol: "http"
```

PHP

```php
// pattern中, protocol采用了 (?<name>)方式, domain采用了 (?P<name>)方式
$pattern = "/(?<protocol>\w+):\/\/(?P<domain>\w+\.\w+\.\w+)/";
preg_match_all($pattern, "http://www.baidu.com", $matches);
print_r($matches);

//结果
Array
(
    [0] => Array
        (
            [0] => http://www.baidu.com
        )
    [protocol] => Array
        (
            [0] => http
        )
    [1] => Array
        (
            [0] => http
        )
    [domain] => Array
        (
            [0] => www.baidu.com
        )

    [2] => Array
        (
            [0] => www.baidu.com
        )
)
```

### 后向引用

将前面某一分组匹配的结果作为新的匹配项

```
ab(\w{2})(\d+)\2\1
```

`abcd22cd` abcd22cc abcd21cd

### 零宽断言

零宽度的匹配，它匹配到的内容不会保存到匹配结果中去，最终匹配结果只是一个位置而已

1）=号的，是肯定匹配

2）< 号的 ，是写在前面的，取后面的值

3）！号的 ，是否定的

4）不带<号的，是写在后面的，取前面的值

一共4种

1）(?=xxx) 

例：\b\w+(?=ing\b)，匹配以ing结尾的单词的前面部分

2）(?<=xxx) 

例：(?<=\bre)\w+\b会匹配以re开头的单词的后半部分

3）(?!xxx) 

例：\d{3}(?!\d)匹配三位数字，而且这三位数字的后面不能是数字

4）(?<!xxx) 

例：(?<![a-z])\d{7}匹配前面不是小写字母的七位数字

简单表达就是 ：

等是叹否，有尖前取后，无尖后取前

### Python中的re模块

1）findall

从字符串中查找出所有符合的字符串，并将结果放到列表中

```python
import re

str = '''
aaaa
time=2021-2-23 17:55:22 operation=修改文章 operator=admin
time=2021-2-23 17:55:22 operation=修改标签 operator=user1
'''

pattern = re.compile(r'time=(.*?)\soperation=(.*?)\soperator=(.*)')
m = pattern.findall(str)
print(m)
```

结果

```
[('2021-2-23 17:55:22', '修改文章', 'admin'), ('2021-2-23 17:55:22', '修改标签', 'user1')]

Process finished with exit code 0
```

2）match

只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None

```python
import re

str = 'time=2021-2-23 17:55:22 operation=修改文章 operator=admin'

pattern = re.compile(r'time=(.*?)\soperation=(.*?)\soperator=(.*)')
m = pattern.match(str)
print(m)
if m is not None:
    print(m.groups())
```

结果

```
<_sre.SRE_Match object; span=(0, 53), match='time=2021-2-23 17:55:22 operation=修改文章 operator=a>
('2021-2-23 17:55:22', '修改文章', 'admin')

Process finished with exit code 0
```

3）search

匹配整个字符串，直到找到一个匹配才返回，未找到返回None

```python
import re

str = '''
aaaa
time=2021-2-23 17:55:22 operation=修改文章 operator=admin
time=2021-2-23 17:55:22 operation=修改标签 operator=user1
'''

pattern = re.compile(r'time=(.*?)\soperation=(.*?)\soperator=(.*)')
m = pattern.search(str)
print(m)
if m is not None:
    print(m.groups())
```

结果

```
<_sre.SRE_Match object; span=(6, 59), match='time=2021-2-23 17:55:22 operation=修改文章 operator=a>
('2021-2-23 17:55:22', '修改文章', 'admin')

Process finished with exit code 0
```




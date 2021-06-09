---
layout: post
title: jsonp原理
tags: jsonp
categories: frontend
---

**原理**

ajax请求受同源策略影响，不允许进行跨域请求，而script标签src属性中的链接却可以访问跨域的js脚本，利用这个特性，服务端不再返回JSON格式的数据，而是返回一段调用某个函数的js代码，将数据作为参数，在src中进行了调用，这样实现了跨域。

---

**实现代码**

`1`服务端

```javascript
//nodejs
var http = require('http');
var url=require('url')
var querystring=require('querystring')
http.createServer(function (req, res) {
    let req_url=url.parse(req.url).pathname//解析出url
    let callback=querystring.parse(url.parse(req.url).query).callback//解析出客户端传过来的回调函数
    let data={
        name:'zhang',
        age:'20'
    }
    data=JSON.stringify(data)//将数据序列化
    if(req_url=='/getscript'){
        res.end(`${callback}(${data})`)//将包裹数据的回调函数返回
    }else{
        res.end("404")
    }
}).listen(3000);

console.log('Server running at http://127.0.0.1:3000/');
```

`2`客户端

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<script>
    function show(data){//定义回调函数，以接收数据
        console.log(data)//{name: "zhang", age: "20"}
    }
</script>
<script src="http://127.0.0.1:3000/getscript?callback=show"></script><!--通过script标签访问-->
<body>
    
</body>
</html>
```


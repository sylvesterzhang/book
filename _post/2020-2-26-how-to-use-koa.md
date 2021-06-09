---
layout: post
title: koa框架使用
tags: nodejs
categories: backend
---
快速上手、路由、动态路由、获取get值、中间间、koa-views使用、ejs小结、利用中间体配置公共变量、获取post数据、静态web服务、koa-art-template使用、cookies使用、session使用、mongodb数据库封装、路由模块化案例、快速创建koa项目koa-generator

**快速上手**

`1`安装

```
npm install koa --save
```

`2`使用

```javascript
var koa=require('koa');//导入模块

var app=new koa()//创建对象

app.use(async(ctx)=>{
    ctx.body="hello"
})

app.listen(3000)
```

---

**路由**

`1`安装

```
npm install koa-roter --save
```

`2`使用路由后的代码

```javascript
var koa=require('koa');
var router=require('koa-router')();//引入并实例化，不需要再new了

var app=new koa()

router.get('/',async(ctx)=>{//ctx,即contex，包含request,response等信息
    ctx.body='首页'//返回数据，相当于response.writeHead(),response.end('首页')
    //ctx.redirect('/')//跳转到'/'
})

app.use(router.routes())//配置路由
app.use(router.allowedMethods())

app.listen(3000)
```

---

**动态路由**

```javascript
router.get('/newscontent/:id',async(ctx)=>{
    console.log(ctx.params)//{ id: '1' }
    ctx.body='新闻内容'
})
```

---

**获取get传值**

运行后，浏览器访问127.0.0.1:3000?name=zhang

```javascript
router.get('/',async(ctx)=>{
    console.log(ctx.query)//{ name: 'zhang' }
    console.log(ctx.querystring)//name=zhang
    console.log(ctx.request.query)//{ name: 'zhang' }
    console.log(ctx.request.querystring)//name=zhang
    ctx.body='首页'
})
```

---

**中间件**

分为应用中间件、路由级中间件、错误处理中间件、第三方中间件

`1`应用中间件

```javascript
//应用中间件
//1.无next参数
app.use(async (ctx)=>{
    ctx.body="你好"//无next参数将不再向下匹配路由
})

//2.有next参数
app.use(async (ctx,next)=>{
    ctx.body="你好"
    await next()//将会向下匹配路由，如果未匹配到则返回"你好"
})
```

`2`路由级中间件

```javascript
router.get('/news',async(ctx,next)=>{
    console.log("第一次匹配")//执行
    ctx.body='第一次匹配结果'//后面同名路由如果有ctx.body，这里不执行，否则执行
    await next()
})

router.get('/news',async(ctx)=>{
    console.log("第二次匹配")//执行
    ctx.body='第二次匹配结果'
})
```

`3`错误处理中间件

```javascript
app.use(async (ctx,next)=>{
    console.log("错误处理中间件")//一次请求中，次句将会被执行两次，类似洋葱模型
    await next()
    if(ctx.status==404){
        ctx.body="找不到页面"
    }else{
        console.log("无错误")
    }
})
```

`4`第三方中间件

其他中间件，需要用app.use进行配置

---

**koa-views使用**

`1`安装

```
npm install koa-views --save
```

```
npm install ejs --save
```

`2`代码

```javascript
var views=require('koa-views')

//注意：配置语句必须放置再路由之前
//app.use(views('./views',{extension:'ejs'}))//html文件的后缀名必须是ejs
app.use(views('./views',{map:{html:'ejs'}}))//html文件的后缀名必须是html

router.get('/',async(ctx)=>{
    await ctx.render('hello',{})
})
```

---

**ejs使用小结**

```html
{% raw %}
<!--引入-->
<%- include('header') -%>

<!--变量-->
<%=name%><!--name是传入的变量-->

<!--解析-->
<%-value%><!--解析value里的html代码-->

<!--循环-->
<%for(item of list){%><!--js写法-->
    <%=item%>
<%}%>
 
<!--判断-->
<%if(num>20){%><!--js写法-->
    <span>num大于20</span>	
<%}%>
{% endraw %}
```

---

**利用中间体配置公共变量**

```javascript
app.use(async(ctx,next)=>{
    ctx.state.userinfo='zhang'
    await next()
})
```

所有html文件均可访问userinfo变量

---

**获取post数据**

`1`原生方式

1）getpostdata.js

```javascript
var querystring=require('querystring');

function getpostdata(ctx){
    return new Promise((resolve,reject)=>{
        try{
            let str=''
            ctx.req.on('data',(chunk)=>{
                str+=chunk
            })
            ctx.req.on('end',()=>{
                str=querystring.parse(str)
                resolve(str)
            })
        }catch(err){
            reject(err)
        }
    })
}
module.exports=getpostdata
```

2）app.js核心代码

```javascript
var getpostdata=require('./getpostdata')

router.post('/login',async(ctx)=>{
    var data=await getpostdata(ctx)
    console.log(data)//[Object: null prototype] { username: 'zhanghuan', password: '123' }
    ctx.response.body='<script>alert("success");history.back()</script>'
})
```

`2`插件方式

1）安装插件

```
npm install koa-bodyparser --save
```

2）核心代码

```javascript
var bodyparser=require('koa-bodyparser')//导入

app.use(bodyparser())//配置,必须放在路由前面

router.post('/login',async(ctx)=>{
    console.log(ctx.request.body)//{ username: 'zhanghuan', password: '123' }
    ctx.response.body='<script>alert("success");history.back()</script>'
})
```

---

**静态web服务**

`1`安装

```
npm instaall koa-static --save
```

`2`核心代码

1）app.js

```javascript
var static=require('koa-static')//导入
app.use(static('./static'))//配置
```

2）html文件中

```html
<link rel="stylesheet" href="css/bootstrap.css">
```

---

**art-template使用**

`1`安装

```
npm install art-template --save
npm install koa-art-template --save
```

`2`核心代码

```javascript
var render=require('koa-art-template')
var path=require('path')

render(app, {
    root: path.join(__dirname, 'views'),   // 视图的位置
    extname: '.ejs',  // 后缀名
    debug: process.env.NODE_ENV !== 'production'  //是否开启调试模式
});

router.get('/login',async(ctx)=>{
    await ctx.render('login',{'title':'title'})
})
```

`3`语法

```html
{% raw %}
<!--引入-->
<%- include('header') -%>
    
<!--变量-->
{{name}}

<!--变量-->
{{name}}
    
<!--变量解析为html-->
{{@html}}
    
<!--判断-->
{{if age>20}}
<p>age大于20</p>
{{else}}
<p>age小于等于20</p>
{{/if}}
   
<!--循环-->
<ul>
	{{each list}}
	<li>{{$index}}--{{$value}}</li>
	{{/each}}
</ul>
{% endraw %}
```

---

**cookies的使用**

1)设置

```javascript
let options={
        maxAge:"1000*60",       //cookie有效时长，单位：毫秒数
        expires:"0000000000",      //过期时间，unix时间戳
        path:"/",         //cookie保存路径, 默认是'/，set时更改，get时同时修改，不然会保存不上，服务同时也获取不到
        domain:".xxx.com",       //cookie可用域名，“.”开头支持顶级域名下的所有子域名
        secure:"false",       //默认false，设置成true表示只有https可以访问
        httpOnly:"true",     //true，客户端不可读取
        overwrite:"true"    //一个布尔值，表示是否覆盖以前设置的同名的 cookie (默认是 false). 如果是 true, 在同一个请求中设置相同名称的所有 Cookie（不管路径或域）是否在设置此Cookie 时从 Set-Cookie 标头中过滤掉。
    }
ctx.cookies.set('name','zhanghuan',options)
```

2）获取

```javascript
var name=ctx.cookies.get('name')
```

3）解决cookie无法用中文的问题

```javascript
//设置cookies
router.get('/setcookies',async(ctx)=>{
    var str='今晚打老虎'
    var buffer=new Buffer(str).toString('base64')
    ctx.cookies.set('name',buffer,{maxAge:1000*60*60,})
    ctx.body="this is setcookies"
})

//获取cookies
router.get('/getcookies',async(ctx)=>{
    var result=new Buffer(ctx.cookies.get('name'),'base64').toString()
    ctx.body="this is getcookies"
})
```

---

**session使用**

`1`安装

```javascript
npm install koa-session --save
```

`2`使用

```javascript
var session=require('koa-session')

const CONFIG = {
    key: 'koa:sess',   //cookie key (default is koa:sess)
    maxAge: 86400000,  // cookie的过期时间 maxAge in ms (default is 1 days)
    overwrite: true,  //是否可以overwrite    (默认default true)
    httpOnly: true, //cookie是否只有服务器端可以访问 httpOnly or not (default true)
    signed: true,   //签名默认true
    rolling: false,  //在每次请求时强行设置cookie，这将重置cookie过期时间（默认：false）
    renew: false,  //(boolean) renew session when session is nearly expired,
 };
 app.keys = ['some secret hurr'];//缺少这一行会报错Error: .keys required for signed cookies
 app.use(session(CONFIG, app));

//设置session
router.get('/setsession',(ctx)=>{
    ctx.session.username='张欢'
    ctx.body='success'
})

//获取session
router.get('/getsession',(ctx)=>{
    console.log(ctx.session.username)
    ctx.body=`${ctx.session.username}`
})
```

---

**mongodb数据库操作封装**

`1`config.js

```javascript
//配置文件
var app={
    dbUrl:'mongodb://localhost:27017',
    dbName:'db01'
}
module.exports=app
```

`2`db.js

```javascript
//在此文件内封装数据库操作的类
var mongoclient=require('mongodb').MongoClient
var ObjectID = require('mongodb').ObjectID
var config=require('./config.js')

class Db{
    static getIncetance(){//开启单例模式
        if(!Db.incetance){
            Db.incetance=new Db()
        }
        return Db.incetance
    }
    constructor(){
        this.connect()
    }
    connect(){
        let _that=this
        return new Promise((resolve,reject)=>{
            if(!_that.dbClient){//解决重复连接问题
                mongoclient.connect(config.dbUrl,(err,client)=>{
                    if(err){
                        reject(err)
                        return
                    }else{
                        resolve(client.db(config.dbName))
                    }
                })
            }else{
                resolve(_that.dbClient)
            }
        })
    }
    find(collection,json={}){
        return new Promise(async(resolve,reject)=>{
            let db=await this.connect()
            let result=db.collection(collection).find(json)
            result.toArray((err,res)=>{
                if(err){
                    console.log(err)
                    reject(err)
                }else{
                    resolve(res)
                }  
            })
        })
    }
    update(collection,json1,json2){
        return new Promise(async(resolve,reject)=>{
            let db=await this.connect()
            db.collection(collection).updateOne(json1,{$set:json2},(err,data)=>{
                if(err){
                    console.log(err)
                    reject(err)
                }else{
                    resolve(data)
                }
            })
        })
    }
    delete(collection,json){
        return new Promise(async(resolve,reject)=>{
            let db=await this.connect()
            db.collection(collection).deleteOne(json,(err,data)=>{
                if(err){
                    console.log(err)
                    reject(err)
                }else{
                    resolve(data)
                }
            })
        })
    }
    insert(collection,json){
        return new Promise(async(resolve,reject)=>{
            let db=await this.connect()
            db.collection(collection).insertOne(json,(err,data)=>{
                if(err){
                    console.log(err)
                    reject(err)
                }else{
                    resolve(data)
                }
            })
        })
    }
    getObjectId(id){
        return new ObjectID(id)
    }
}

module.exports=Db.getIncetance()
```

`3`app.js

```javascript
//封装的类的使用案例
var koa=require('koa')
var router=require('koa-router')()
var db=require('./db.js')

var app=new koa()

app.use(router.routes())//配置路由
app.use(router.allowedMethods())

router.get('/',async(ctx)=>{//查询
    let data=await db.find('user')
    //console.log(data)
    ctx.body=`共有${data.length}条数据`
})

router.get('/insert',async(ctx)=>{//新增
    let data=await db.insert('user',{'name':'王麻子','age':30,'sex':'male','job':'coder'})
    //console.log(data)
    ctx.body=`insert:${data}`
})

router.get('/delete',async(ctx)=>{//删除
    let id=await db.getObjectId('5e4a88fc9ca5885c8806b955')//传入字符串为该条记录的id，将其转换成对象后再进行数据库操作
    let data=await db.delete('user',{'_id':id})
    //console.log(data)
    ctx.body=`delete:${data}`
})

router.get('/update',async(ctx)=>{//更新
    let data=await db.update('user',{'name':'王麻子'},{'name':'王麻子11'})
    console.log(data)
    ctx.body=`update:${data}`
})

app.listen(3000)
```

---

**路由模块化案例**

实际开发中，各个模块需要进行分离

`1`分离思路

```
--package.json
--package-lock.json
--app.js//入口文件
--views//存放html模板文件
	--index.html
--static//存放静态文件
	--css
		--bootstrap.css
		--bootstrap.css.map
--routes//存放路由文件
	--index.js
	--admin.js
--node_modules
--module//存放自定义模块
```

`2`文件代码

1）app.js

```javascript
var koa=require('koa')
var router=require('koa-router')()
var app=new koa()

//静态路由
var static=require('koa-static')
app.use(static('./static'))

//路由
//业务流程均在路由里
var router_index=require('./routes/index.js')/*前台*/
var router_admin=require('./routes/admin.js')/*后台*/
router.use('/',router_index)
router.use('/admin',router_admin)

//模板
//待渲染的html
var render=require('koa-art-template')
var path=require('path')
render(app,{
    root: path.join(__dirname, 'views'),   // 视图的位置
    extname: '.html',  // 后缀名
    debug: process.env.NODE_ENV !== 'production'  //是否开启调试模式
})
app.use(router.routes())
app.use(router.allowedMethods())
app.listen(3000)
//此文件是项目启动的入口文件
```

2）index.js

```javascript
var router=require('koa-router')()

router.get('/',async(ctx)=>{
    ctx.render('index')
})

module.exports=router.routes()
```

3）admin.js

```javascript
var router=require('koa-router')()

router.get('/',async(ctx)=>{
    ctx.body='这是admin管理页面首页'
})

module.exports=router.routes()
```

4）index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="/css/bootstrap.css">
</head>
<body>
    <h1>this is index</h1>
</body>
</html>
```

---

**快速创建koa项目koa-generator**

`1`全局安装

```
npm install koa-generator -g
```

`2`创建项目

```
koa koa-demo
```

`3`安装依赖

```
cd ko-demo
```

```
npm install
```

`4`启动项目

```
npm start
```




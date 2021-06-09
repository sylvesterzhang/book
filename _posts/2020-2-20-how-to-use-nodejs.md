---
layout: post
title: nodejs基础知识
tags: nodejs
categories: backend
---

vs插件安装、快速运行、url模块使用、自动重启工具supervisor、自定义模块、下载第三方包、fs模块、Async、Await的使用、文件里、简易静态服务器、事件驱动EventEmitter、模拟登陆简单版、模拟express封装、操作mongodb数据库

**VScode插件安装**

需要安装Node Snippets插件才会有提示

---

**快速运行**

```javascript
var http=require('http')
http.createServer((req,res)=>{
    //req 请求对象，可获取请求的url等信息
    //res 响应对象
    console.log(req.url)
    res.writeHead(200,{"Content-type":"text/html;charset='utf-8"})
    res.write("<head><meta charset='UTF-8'></head>")//解决乱码
    res.write("你好，hello")//向前台返回内容
    res.end()//结束响应，注释掉前台将会一直请求
}).listen(3000)
```

---

**url模块的使用**

```javascript
//对于get方式传值好用
const url=require('url')
let api="www.zhanghuan.top?name=zhanghuan&age=20"
let parse_reasult=url.parse(api,true).query
console.log(parse_reasult)//[Object: null prototype] { name: 'zhanghuan', age: '20' }
```

---

**自动重启动工具supervisor**

1）安装

```
npm install -g supervisor
```

2）使用supervisor代替node命令启动应用

```
supervisor server.js
```

---

**自定义模块**

新建一个文件夹module，里面放置js文件，一个js文件就是一个自定义模块，例如在module里新建request.js文件

1）创建模块

```javascript
//request.js里的代码
class request{
    static get(){
        console.log("get方法")
    }
    static post(){
        console.log("post方法")
    }
}
//对外暴露写法1，选择性暴露
//exports.request=request

//对外暴露写法2，将整个对象暴露
module.exports=request
```

2）导入模块

```javascript
//在modules外部app.js中
var req=require('./modules/request')
req.get()//输出 get方法
```

3）node_modules系统方式自定义模块

新建一个node_modules的文件夹，里面建一个axios文件夹，里面新建一个index.js的文件，那么在外部（与node_modules同级的js文件中）可直接使用`require('./axios')`导入文件

注意：如果axios里的js文件命名不是index.js，用这种方法导入会报错。解决办法是，在axios文件里打开命令窗口，输入`npm init --yes`

---

**下载第三方包**

`1`初始化

```
npm init --yes
```

生成配置文件package.json,package-lock.json

`2`安装包

例如我们要安装MD5的包

```
npm install --save
```

注意：这里的--save会将安装的包名记录到配置文件中，别人拿到配置文件后运行命令`npm install`便可安装所有依赖

指定包的版本安装

```
npm install jquery@1.8.0
```

`3`常见的包

silly-datetime(日期格式化)

mkdirp(创建文件)

`4`删除模块

1)简单方法

```
npm uninstall md5
```

2)另一种方法

在package.json中的dependencies属性下，删除掉该模块，然后删除掉node_modules文件夹，再重新执行`npm install`

`5`配置文件里dependencies属性里的表示符

```
"dependencies": {
    "md5": "^2.2.1"
  }
```

^表示第一位版本号不变，后面两位取最新

~表示前两位不变，最后一个取最新

*表示全部取最新

---

**fs模块**

`1`fs.stat('dir',callback)判断路径是否存在

```javascript
fs.stat(path,(err,data)=>{
    if(err){
        console.log(err)
    }else{
        //data是一个名为status的对象，这个对象有isDirectory()和isFile()方法，调用以查看该路径是否是文件
        console.log(data.isDirectory())
        console.log(data.isFile())
    }
})
```

`2`fs.mkdir('dir',callback)创建路径

`3`fs.writeFile('dir','file.content',callback)新建文件

`4`fs.appendFile('dir','append content',callback)文件追加内容

`5`fs.readFile('dir',callback)

`6`fs.readDire('dir',callback)阅读目录下包含的所有文件和文件夹

`7`fs.rename('dir','new dir',callback)重命名或移动文件

`8`fs.rmdir('dir',callback)删除路径，前提是路径下不包含任何文件和文件夹

`9`fs.unlin('dir',callback)删除路径下的文件

例子：使用回调函数取出目标路径下所有文件的文件名

```javascript
const fs=require('fs')
var dir='../../../desktop'//查找的地址
var lis=[]//查找到的文件放置在这里
function get_filename(dir){
    fs.readdir(dir,(err,data)=>{
        if(err){
            console.log(err)
            return
        }else{
            if(data==[]){
                return
            }
            (function getdir(i){
                if(i==data.length){
                    return
                }
                var path=dir+'/'+data[i]
               fs.stat(path,(err,status)=>{
                    if(status.isFile()){//如果传过来的是文件，添加到lis中
                        lis.push(path)
                    }else{//如果传过来的是路径，进行递归
                        get_filename(path)
                    }
               })
                getdir(i+1)
            })(0)
            //这里不可以用for循环迭代，因为fs是异步的,用递归方式替代for循环
        }
    })
}
get_filename(dir)
var t=setInterval(()=>{
    console.log(lis)//因为fs是异步的，所以会执行更长时间，这里等待3秒后输出结果
    clearInterval(t)
},3000)
```

---

**Async、Await的使用**

async是声明一个异步的function，await是等待一个异步function的执行完成并获取异步function返回的数据，注意await外部包裹的方法必须是异步方法（扩展：所有返回为Promise的函数都可以用await获取其resolve的值）

```javascript
async function test(){
    return new Promise((resolve,reject)=>{
        t=setInterval(()=>{       
            clearInterval(t)
            resolve("你好")
        },3000)
    })
    
}

async function main(){
    var data=await test()//外部必须是异步函数包裹
    console.log(data)//3秒后执行这一行，和下面这一行
    console.log("hello")
}
main()

```

取出目标路径下所有文件的文件名异步版本

```typescript
const fs=require('fs')
var dir='../../../desktop'//查找的地址
var lis=[]//查找到的文件放置在这里
async function dir_isFile(path){
    //@params path路径
    //根据路径判断是否是文件，如果是文件返回真
    return new Promise((resolve,reject)=>{
        fs.stat(path,(err,status)=>{
            if(err){
                console.log(err)
            }
            resolve(status.isFile())
        })
    })
}
function get_filename(dir){
    fs.readdir(dir,async (err,data)=>{//函数内需要使用await，所以必须定义成异步函数
        if(err){
            console.log(err)
            return
        }else{
            if(data==[]){
                return
            }
            for(var item of data){
                var path=dir+'/'+item
                if(await dir_isFile(path)){
                    lis.push(path)
                }else{
                    get_filename(path)
                }
            }
           
        }
    })

}

get_filename(dir)
var t=setInterval(()=>{
    console.log(lis)
    clearInterval(t)
},10000)   
```

---

**文件流**

`1`读取流

```javascript
const fs=require('fs')
var readstream=fs.ReadStream('new_file','utf-8')
var num=0//记录读取次数
readstream.on('data',(data)=>{
    console.log(data)//输出读取的数据
    num++//每读取一次加一
})
readstream.on('end',()=>{
    console.log("--end--")
    console.log(num)//输出10，说明读取了10次
})
readstream.on('error',(err)=>{
    console.log(err)//读取过程中出现错误就输出错误
})
```

`2`写入流

```javascript
const fs=require('fs')
var writestream=fs.WriteStream('new_file')
for(var i=0;i<10000;i++){
    writestream.write('阅读使人充实，会谈使人敏捷，写作使人精确。\n')
}
writestream.end()//标记末尾，未标记末尾的文件不可以触发以下的完成事件
writestream.on('finish',()=>{
    console.log("写入完成")
})
```

`3`管道流

```javascript
const fs=require('fs')
var readstream=fs.ReadStream('new_file')
var writestream=fs.WriteStream('./files/new_file1')
readstream.pipe(writestream)//通过管道将new_file文件复制到'./files/new_file'里，注意复制前files文件夹必须存在
```

---

**简易静态web服务器**

```javascript
var http = require('http');
var fs=require('fs');
var url=require('url');
var path=require('path');

http.createServer(function (request, response) {
    var req_path=request.url;
    if(req_path!='/favicon.ico'){
        var pathname='.'+url.parse(req_path).pathname
        var ext_name=path.extname(pathname)//获取后缀名
        fs.readFile(pathname,(err,data)=>{
            if(err){
                fs.readFile('./static/404.html',(err,data)=>{
                    response.writeHead(200, {'Content-Type': "text/html"});
                    response.write(data)
                    response.end();
                })
            }else{
                var head_obj=JSON.parse(fs.readFileSync('./static/mime.json').toString())//根据后缀名匹配出返回的请求头
                if(ext_name in head_obj){
                    var content_type=head_obj[ext_name]
                }else{
                    var content_type='text/plain'
                }
                response.writeHead(200, {'Content-Type': content_type});
                response.write(data)
                response.end();
            }
        })
    }
  

  
}).listen(8081);

console.log('Server running at http://127.0.0.1:8081/');
```

---

**事件驱动EventEmitter**

```javascript
var events=require('events');
var EventEmitter=new events.EventEmitter();

EventEmitter.on('data1',(data)=>{
    console.log(data)
})

EventEmitter.emit('data1',"this is data")//this is data
```

---

**模拟登陆简单版**

`1`app.js文件

```javascript
var http = require('http');
var ejs=require('ejs');
var url=require('url');
var querystring=require('querystring')

http.createServer(function (request, response) {
    if(request.url!="/favicon.ico"){
        var pathname=url.parse(request.url).pathname
        if(pathname='/login'){
            if(request.method=='GET'){
                ejs.renderFile('./templates/login.ejs',{},(err,data)=>{
                    response.end(data)
                })
            }else{
                var posstr=""
                request.on('data',chunk=>{
                    posstr+=chunk
                })
                request.on('end',()=>{
                    var data_obj=querystring.parse(posstr)
                    response.writeHead(200,{'Content-Type': "text/html"})
                    if(data_obj['username']=='zhanghuan'&&data_obj['password']=='123'){
                        response.end("<script>alert('suceess');history.back()</script>")
                    }else{
                        response.end("<script>alert('username or password is not correct');history.back()</script>")
                    }
                    
                })
            }
            
        }
    }
  
}).listen(8081);

console.log('Server running at http://127.0.0.1:8081/');
```

`2`模板文件login.ejs

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <p>login</p>
    <form action="/login" method="post">
        <input type="text" placeholder="用户名" name="username">
        <input type="text" placeholder="密码" name="password">
        <button>提交</button>
    </form>
    
</body>
</html>
```

---

**模拟express的简单封装**

`1`封装文件server.js代码

```javascript
var url=require('url');
var server=function(){
    var G=this
    G._get={}
    G._post={}

    var app=function(req,res){
        var pathname=url.parse(req.url).pathname+'/'
        if(pathname=='/favicon.ico/'){
            return
        }
        if(!G._get[pathname]){//如果没有定义该路由，直接返回
            res.end("no such router")
        }
        if(req.method=='GET'){
            try {
                G._get[pathname](req,res)//执行_get里对应的方法
            } catch (error) {
                res.end("err")
            }
        }
        if(req.method=='POST'){
            try {
                var str=""
                req.on('data',(chunk)=>{
                    str+=chunk
                })
                req.on('end',()=>{
                    req.body=str
                    G._post[pathname](req,res)//执行_post里对应的方法
                })
                
            } catch (error) {
                res.end("err")
            }
        }
    }

    app.get=function(string,callback){
        if(!string.startsWith('/')){
            string='/'+string
        }
        if(!string.endsWith('/')){
            string=string+'/'
        }
        if(string=='/'){
            string='//'
        }
        G._get[string]=callback
    }

    app.post=function(string,callback){
        if(!string.startsWith('/')){
            string='/'+string
        }
        if(!string.endsWith('/')){
            string=string+'/'
        }
        if(string=='/'){
            string='//'
        }
        G._post[string]=callback
    }

    return app
}

module.exports=server()
```

`2`文件app.js

```javascript
var http = require('http');
var app=require('./server.js');
var ejs=require('ejs');
var querystring=require('querystring')

http.createServer(app).listen(8081);

app.get('/',(req,res)=>{
    res.writeHead(301, {'Location': '/login'});//实现重定向
    res.end();
})

app.get('/login',(req,res)=>{
    ejs.renderFile('./templates/login.ejs',{},(err,data)=>{
        res.end(data)
    })
})
app.post('/login',(req,res)=>{
    var data_obj=querystring.parse(req.body)
    if(data_obj['username']=='zhanghuan'&&data_obj['password']=='123'){
        res.end("<script>alert('suceess');history.back()</script>")
    }else{
        res.end("<script>alert('username or password is not correct');history.back()</script>")
    }
})
console.log('Server running at http://127.0.0.1:8081/');

```

---

**操作mongodb数据库**

`1`安装模块

```
npm install mongodb --save-dev
```

`2`导入模块并连接

```javascript
var MongoClient=require('mongodb').MongoClient;
MongoClient.connect('mongodb://localhost:27017',(err,client)=>{
     if(err){
         console.log(err)
     }
     var db=client.db("db01")//连接到db01数据库
     //所有关于数据库db01的操作都对db发起即可
     //db.collection('user')
     //拿到user表，可对其进行增删改查操作
 })
```

`3`对表格的操作

1)新增

```javascript
var insert_obj={'name':'zhanghua','age':21]}
db.collection('user').insertOne(insert_obj,(err,data)=>{
	if(err){
		console.log(err)
	}
	res.end("insert success")
})
```

2)删除

```javascript
db.collection("user").deleteOne({'name':'zhanghuan'},(err,data)=>{
	if(err){
		console.log(err)
	}
	res.end("delete success")
})
```

3)修改

```javascript
var update_obj={'name':'zhanghuan123'}
db.collection("user").updateOne({'name':'zhanghuan'},{$set:update_obj},(err,data)=>{
	if(err){
		console.log(err)
	}
	res.end("update success")
})
```

4)查找

```javascript
db.collection('user').find({}).toArray((err,result)=>{
	client.close()
	console.log(result)//拿到的结果会以数组形式存在result里         
})
```

`4`用户列表增删改查的案例

这里是在模拟的express框架下进行操作

1）app.js文件

```javascript
var http = require('http');
var app=require('./server.js');
var ejs=require('ejs');
var querystring=require('querystring');
var url=require('url');
var querystring=require('querystring');
var MongoClient=require('mongodb').MongoClient;
var ObjectID = require('mongodb').ObjectID;
var db_url='mongodb://localhost:27017';

http.createServer(app).listen(8081);

//用户列表
app.get('/user',(req,res)=>{
    MongoClient.connect(db_url,(err,client)=>{
        if(err){
            console.log(err)
            res.end('db error')
        }
        var db=client.db("db01")
        
        db.collection('user').find({}).toArray((err,result)=>{
            client.close()
            ejs.renderFile('./templates/content.ejs',{'list':result},(err,data)=>{
                if(err){
                    console.log(err)
                }
                res.end(data)
            })            
        })
        
    })
    
})

//删除用户
app.get('/userdel',(req,res)=>{
    MongoClient.connect(db_url,(err,client)=>{
        if(err){
            console.log(err)
            res.end('db error')
        }
        var db=client.db("db01")
        var _id=querystring.parse(url.parse(req.url).query)['id']
        _id=new ObjectID(_id)
        db.collection("user").deleteOne({'_id':_id},(err,data)=>{
            if(err){
                console.log(err)
                res.end('delete failure')
            }
            res.end("delete success")
        })
    })
})

//新增用户
app.get('/adduser',(req,res)=>{
    ejs.renderFile('./templates/adduser.ejs',{'obj':''},(err,data)=>{
        res.end(data)
    })
})
app.post('/adduser',(req,res)=>{
    var data_obj=querystring.parse(req.body)
    MongoClient.connect(db_url,(err,client)=>{
        if(err){
            console.log(err)
            res.end('db error')
        }
        var db=client.db("db01")
        var insert_obj={'name':data_obj['name'],'age':data_obj['age'],'sex':data_obj['sex'],'job':data_obj['job']}
        db.collection('user').insertOne(insert_obj,(err,data)=>{
            if(err){
                console.log(err)
                res.end('insert failure')
            }
            res.end("insert success")
        })
    })
})

//更新用户
app.get('/userupdate',(req,res)=>{
    MongoClient.connect(db_url,(err,client)=>{
        if(err){
            console.log(err)
            res.end('db error')
        }
        var db=client.db("db01")
        var _id=querystring.parse(url.parse(req.url).query)['id']
        _id=new ObjectID(_id)
        db.collection('user').find({'_id':_id}).toArray((err,result)=>{
            client.close()
            ejs.renderFile('./templates/adduser.ejs',{'obj':result[0],'current_url':req.url},(err,data)=>{
                if(err){
                    console.log(err)
                }
                res.end(data)
            })            
        })
        
    })
})
app.post('/userupdate',(req,res)=>{
    var data_obj=querystring.parse(req.body)
    MongoClient.connect(db_url,(err,client)=>{
        if(err){
            console.log(err)
            res.end('db error')
        }
        var db=client.db("db01")
        var _id=querystring.parse(url.parse(req.url).query)['id']
        _id=new ObjectID(_id)
        var update_obj={'name':data_obj['name'],'age':data_obj['age'],'sex':data_obj['sex'],'job':data_obj['job']}
        db.collection("user").updateOne({'_id':_id},{$set:update_obj},(err,data)=>{
            if(err){
                console.log(err)
                res.end('update failure')
            }
            res.end("update success")
        })
    })
})

console.log('Server running at http://127.0.0.1:8081/');

```

2）adduser.ejs

```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>add or update</title>
</head>
<body>
    <%if(obj){%>
        <form action=<%=current_url%> method="post">
            <input type="text" name='name' placeholder="name" required value=<%=obj.name%>>
            <input type="text" name='age' placeholder="age" required value=<%=obj.age%>>
            <input type="text" name='sex' placeholder="sex" required value=<%=obj.sex%>>
            <input type="text" name='job' placeholder="job" required value=<%=obj.job%>>
            <button>提交</button>
        </form>
    <%}%>
    <%if(!obj){%>
        <form action="/adduser" method="post">
            <input type="text" name='name' placeholder="name" required>
            <input type="text" name='age' placeholder="age" required>
            <input type="text" name='sex' placeholder="sex" required>
            <input type="text" name='job' placeholder="job" required>
            <button>提交</button>
        </form>
   <% }%>
    
</body>
</html>
{% endraw %}
```

3）content.ejs

```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>userlist</h1>
    <table>
        <tr>
            <th>name</th>
            <th>age</th>
            <th>sex</th>
            <th>job</th>
            <th>operation</th>
        </tr>
        <%for(item of list){%>
            <tr>
                <td><%=item.name%></td>
                <td><%=item.age%></td>
                <td><%=item.sex%></td>
                <td><%=item.job%></td>
                <td><a href="userdel?id=<%=item._id%>">删除</a><a href="userupdate?id=<%=item._id%>">更新</a></td>
            </tr>
        <%}%>
    </table>
    <br>
    <button><a href="/adduser">新增用户</a></button>
</body>
</html>
{% endraw %}
```


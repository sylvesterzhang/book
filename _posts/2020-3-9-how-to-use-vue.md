---
layout: post
title: vue使用
tags: vue
categories: frontend
---

快速开始、挂载点el、数据对象data、方法对象methods、渲染标识、插值表达式闪烁问题、过滤器、自定义全局按键修饰符、人员管理实现案例、自定义指令、声明周期函数、Vue-resource向服务端请求数据、用户管理案例、组件、ref获取dom节点、路由、watch的监视功能、computed存放组合计算后的变量、nrm的使用、render函数渲染、wepack中使用vue

**快速开始**

```javascript
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

```html
{% raw %}
<div id="app">
  {{ message }}<!--Hello Vue!-->
</div>
{% endraw %}
```

其中，el为挂载点，data为数据对象，methods为方法对象

---

**挂载点el**

vue命中元素及其内部的后代元素，不可将html、body作为挂载对象，可通过id、class、tag作为选择挂载点

---

**数据对象data**

定义数据变量，模板中直接使用变量便可取到变量的值。但在methods中，需要在变量前加上this.

---

**方法对象methods**

定义方法

---

**渲染标识**

`1`文本

v-text和v-html，两者的区别是，v-text的值不会被浏览器二次渲染，v-html则会

`2`事件

v-on:eventname，其中eventname可以是click（点击），mouseoner（鼠标移入）,dbclick（双击），值为methods里的方法变量

可以将事件进行简写，v-on:click可简写为@click，其他类似

*事件修饰符*

|  修饰符  | 功能                             |
| :------: | -------------------------------- |
|  .stop   | 阻止冒泡                         |
| .prevent | 阻止默认事件                     |
| .capture | 实现捕获触发事件机制             |
|  .self   | 实现只点击当前元素时，才触发事件 |
|  .once   | 只能触发一次事件处理函数         |

`3`显示隐藏

v-if，v-show，值为布尔值，区别为前者会进行dom操作，后者是样式操作

`4`设置元素属性

v-bind:attr，其中attr可以是class或其他赋予标签的属性

可以进行简写，v-bind:class可简写为:class，其他类似

```html
{% raw %}
<!--通过类名设置样式-->
<h1 class='red thin'>天生我材必有用，千金散尽还复来。</h1>
<h1 :class="['red','thin']">天生我材必有用，千金散尽还复来。</h1>
<h1 :class="[flag?'active':'']">天生我材必有用，千金散尽还复来。</h1><!--flag为布尔值-->
<h1 :class="{'active':flag}">天生我材必有用，千金散尽还复来。</h1>
{% endraw %}
```

```html
{% raw %}
<!--通过style属性设置样式-->
<h1 :style="{color:'red'}">北国风光，千里冰封，万里雪飘。</h1>
<h1 :style='[obj1,obj2]'>北国风光，千里冰封，万里雪飘。</h1><!--obj1,obj2都是data里的变量对象-->
{% endraw %}
```

`5`循环

v-for，值为循环语句如`item of arr`，将循环标签内包含item的标签

```html
{% raw %}
<ul v-for="item of arr">
    <li>{{item}}</li>
</ul>
{% endraw %}
```

`6`双向数据绑定

v-model，值为data里的变量，但只可用在表单元素中

---

**插值表达式闪烁问题**

标签内包含插值表达式（如{{item}}），会在渲染时出现闪烁问题，通过v-cloak可以解决

```html
{% raw %}
<style>
[v-cloak]{
	display: none;
}
</style>
<p v-cloak>
    {{name}}
</p>
{% endraw %}
```

---

**过滤器**

`1`全局过滤器

```html
{% raw %}
<p>{{msg|msgFormat}}</p>
<script>
Vue.filter('msgFormat',function(msg){
    return msg.replace(/单纯/g,'邪恶')
})
</script>
{% endraw %}
```

`2`局部过滤器

```javascript
var app1=new({
    el:'#app1',
    data:{},
    methods:{},
    filters:{
        msgFormat:function(msg){
            return msg.replace(/单纯/g,'邪恶')
        }
    }
})
```

---

**自定义全局按键修饰符**

```javascript
vue.config.keyCodes.f2=113
```

---

**人员管理实现案例**

实现人员添加、删除、搜索功能

```html
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        [v-cloak]{
            display: none;
        }
    </style>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
    window.onload=function(){
        var app=new Vue({
            el:'#app',
            data:{
                id:'',
                name:'',
                age:'',
                sex:'',
                search:'',
                list:[{id:'1',name:'zhang',age:'20',sex:'male'}]
            },
            methods:{
                doadd(){//添加
                    if(!this.name||!this.age||!this.sex){
                        return
                    }
                    let dic={id:this.id,name:this.name,age:this.age,sex:this.sex}
                    this.list.push(dic)
                },
                del(i){//删除
                    this.list.splice(i,1)
                },
                dosearch(){//搜索
                    return this.list.filter(item=>{
                        if(item.name.includes(this.search)){
                            return item
                        }
                    })
                }
            }
        })
    }
    </script>
</head>
<body>
    <div id="app">
        <div class="container">
            <p></p>
            <div class="card">
                <div class="card-body">
                    <form class="form-inline mx-sm-3" action="">
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='id' placeholder="id">
                        </div>
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='name' placeholder="name">
                        </div>
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='age' placeholder="age">
                        </div>
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='sex' placeholder="sex">
                        </div>
                        <button class="btn btn-info" @click.prevent="doadd">add</button>
                    </form>
                    <br>
                    <form action="" class="form-inline mx-sm-3">
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='search' placeholder="search">
                        </div>
                    </form>
                </div>
            </div>
            <p></p>
            <table class="table">
                <thead class="thead-dark">
                  <tr>
                    <th scope="col">Id</th>
                    <th scope="col">Name</th>
                    <th scope="col">Age</th>
                    <th scope="col">Sex</th>
                    <th scope="col">Operating</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for='(item,i) in list' v-cloak v-show="search==''">
                    <td scope="row">{{item.id}}</td>
                    <td>{{item.name}}</td>
                    <td>{{item.age}}</td>
                    <td>{{item.sex}}</td>
                    <td><button @click="del(i)" class="btn btn-sm btn-info">delete</button></td>
                  </tr>
                  <tr v-for='(item,i) in dosearch()' v-cloak v-show="search!=''">
                    <td scope="row">{{item.id}}</td>
                    <td>{{item.name}}</td>
                    <td>{{item.age}}</td>
                    <td>{{item.sex}}</td>
                    <td><button @click="del(i)" class="btn btn-sm btn-info">delete</button></td>
                  </tr>
                </tbody>
              </table>
        </div>
      </div>
    
</body>
</html>
{% endraw %}
```

---

**自定义指令**

`1`全局指令

```javascript
Vue.directive('color',{
    bind:function(el,binding){
        //样式相关的在这里定义
        el.style.color=binding.value
    },
    inserted:function(){
        //js行为相关操作在这里定义
    },
    updated:function(){
        
    }
})
```

```html
<input type="text" class="form-control" v-model='search' placeholder="search" v-color="'red'">
```

`2`局部指令

```javascript
var app1=new({
    el:'#app1',
    data:{},
    methods:{},
    filters:{},
    directives:{
        'title':{
            bind:function(el,binding){
                el.title=binding.value
            },
            inserted:function(){  
            },
            updated:function(){
            }
        }
    }
})
```

```html
<button class="btn btn-info" @click.prevent="doadd" v-title="'button add'">add</button>
```

`3`简写

在`bind`和`updated`做重复操作

```javascript
Vue.directive('color',function(el,binding){
    el.style.color=binding.value
})
```

---

**声明周期函数**

Vue实例在创建到销毁过程中，各个阶段会触发执行一个函数

```javascript
var app1=new({
    el:'#app1',
    data:{},
    methods:{},
    /*运行前*/
	beforeCreate(){
        //在实例完全被创建出来之前会执行，data,methods中的数据还未初始化
        console.log("h1")
    },
    created(){
        //实例创建完成执行，data,methods中的数据最早可在这里操作
        console.log("h2")
    },
    beforeMount(){
        //模板在内存中编译完成，但未注入到页面中
        console.log("h3")
    },
    mounted(){
        //实例已完全创建好，如果要操作dom节点，最早在此函数中
        console.log("h4")
    },
    /*运行中*/
    beforeUpdate(){
        //执行时，页面中的数据时旧的，而data中数据时最新的，页面和最新数据尚未同步
        console.log("h5")
    },
    updated(){
        //执行时，页面后data中的数据已保持同步
        console.log("h6")
    },
    /*销毁过程*/
    beforeDestory(){
        //执行时，实例上所有的数据方法均可用，还未真正执行销毁
        console.log("h7")
    },
    destroyed(){
        //执行时，组件中所有的数据和方法已被销毁
        console.log("h8")
    }
})
```

---

**Vue-resource向服务端请求数据**

`1`引入模块

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://cdn.staticfile.org/vue-resource/1.5.1/vue-resource.min.js"></script>
<!--vue-resource依赖于vue-->
```

`2`GET请求

```javascript
this.$http.get('http://127.0.0.1:3000/userlist'/*地址*/，{params:{name:'zhang'}}/*参数*/).then(data=>{
    this.list=data.body//数据在data.body里
})
```

`3`POST请求

```javascript
this.$http.post('http://127.0.0.1:3000/deluser',{id:this.list[i]._id},{emnlateJSON:true}).then(data=>{
    if(data.body.stat){
        this.list.splice(i,1)
    }
})
```

`4`JSONP请求

```javascript
this.$http.jsonp(this.url).then(result=>{
    console.log(result.body)
})
```

注意，该模块的GET和POST都不支持跨域，需要跨域使用axios

`5`启用服务器根设置

```javascript
//为vue设置根地址，发送请求时不用填写ip地址和端口号，但目标url不可以‘/’开头
Vue.http.options.root='http://127.0.0.1:3000'

//启用emulateJSON选项，使得服务器能够如解析表单一样进行解析
Vue.http.options.emnlateJSON=true
```

---

**用户管理案例**

`1`前端页面

```html
<!--文件名hello.html-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        [v-cloak]{
            display: none;
        }
    </style>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://cdn.staticfile.org/vue-resource/1.5.1/vue-resource.min.js"></script>
    <script>
    window.onload=function(){
       Vue.http.options.root='http://127.0.0.1:3000'
       Vue.http.options.emnlateJSON=true
        var app=new Vue({
            el:'#app',
            delimiters:['$$','$$'],//避免和后端的模板语言发生冲突
            data:{
                id:'',
                name:'',
                age:'',
                sex:'',
                search:'',
                list:[],
            },
            created(){
                this.getdata()
            },
            methods:{
                doadd(){//添加
                    if(!this.name||!this.age||!this.sex){
                        return
                    }
                    let dic={name:this.name,age:this.age,sex:this.sex}
                    this.$http.post('adduser',dic).then(data=>{
                        if(data.body.stat){
                            this.getdata()
                        }
                    })       
                },
                del(i){//删除
                    this.$http.post('deluser',{id:this.list[i]._id}).then(data=>{
                        if(data.body.stat){
                            this.list.splice(i,1)
                        }
                    })       
                },
                dosearch(){//搜索
                    return this.list.filter(item=>{
                        if(item.name.includes(this.search)){
                            return item
                        }
                    })
                },
                getdata(){
                    this.$http.get('userlist').then(data=>{
                    this.list=data.body
                })
                }
            }
            
        })
    }
    </script>
</head>
<body>
    <div id="app">
        <div class="container">
            <div class="card">
                <div class="card-body">
                    <form class="form-inline mx-sm-3" action="">
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='name' placeholder="name">
                        </div>
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='age' placeholder="age">
                        </div>
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='sex' placeholder="sex">
                        </div>
                        <button class="btn btn-info" @click.prevent="doadd" >add</button>
                    </form>
                    <br>
                    <form action="" class="form-inline mx-sm-3">
                        <div class="form-inline form-group mx-sm-3">
                            <input type="text" class="form-control" v-model='search' placeholder="search">
                        </div>
                    </form>
                </div>
            </div>
            <p></p>
            <table class="table">
                <thead class="thead-dark">
                  <tr>
                    <th scope="col">Id</th>
                    <th scope="col">Name</th>
                    <th scope="col">Age</th>
                    <th scope="col">Sex</th>
                    <th scope="col">Operating</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for='(item,i) in list' v-cloak v-show="search==''">
                    <td scope="row">$$item._id$$</td>
                    <td>$$item.name$$</td>
                    <td>$$item.age$$</td>
                    <td>$$item.sex$$</td>
                    <td><button @click="del(i)" class="btn btn-sm btn-info">delete</button></td>
                  </tr>
                  <tr v-for='(item,i) in dosearch()' v-cloak v-show="search!=''">
                    <td scope="row">$$item._id$$</td>
                    <td>$$item.name$$</td>
                    <td>$$item.age$$</td>
                    <td>$$item.sex$$</td>
                    <td><button @click="del(i)" class="btn btn-sm btn-info">delete</button></td>
                  </tr>
                </tbody>
              </table>
        </div>
      </div>
    
</body>
</html>
```

`2`后端

```javascript
const koa=require('koa')
const router=require('koa-router')()
const url=require('url')
var querystring=require('querystring')
var render=require('koa-art-template')
var path=require('path')
var axios=require('axios')
var db=require('./db.js')//自封装的mongodb数据库操作类
var bodyParser = require('koa-bodyparser');

var app=new koa()
app.use(bodyParser())


render(app, {
    root: path.join(__dirname, 'views'),   // 视图的位置
    extname: '.html',  // 后缀名
    debug: process.env.NODE_ENV !== 'production'  //是否开启调试模式
});


router.get('/person_m',async(ctx)=>{//获取界面
    await ctx.render('hello',{})
})
router.get('/userlist',async(ctx)=>{//获取用户列表接口
    let result=await db.find('userlist')
    ctx.body=result
})
router.post('/deluser',async(ctx)=>{//删除用户接口
    let id=ctx.request.body['id']
    console.log(id,db.getObjectId(id))
    await db.delete('userlist',{'_id':db.getObjectId(id)}).then((data)=>{
        ctx.body={stat:1}
    })
})
router.post('/adduser',async(ctx)=>{//新增用户接口
    let data=ctx.request.body
    await db.insert('userlist',data).then((data)=>{
        ctx.body={stat:1}
    })
})
app.use(router.routes())//配置路由
app.use(router.allowedMethods())

app.listen(3000)
```

---

**组件**

模块化是从代码角度将项目进行划分，而组件化是从UI角度对项目进行划分

`1`创建渲染公共组件

1）直接声明方式

```javascript
var com1=Vue.extend({
    template:'<h1>hahahha</h1>'
  })//构建组件
  Vue.component('mycom1',com1)//将组件注册到全局
```

```html
<div id="app">
  <mycom1></mycom1><!--将组件渲染出来-->
</div>
```

2）直接声明方式简化

```
  Vue.component('mycom2',{
  	template:'<h1>aaaaaa</h1>'
  })//将组件注册到全局
```

渲染同上

3）template标签方式

```html
<template id="com3"><!--创建标签-->
  <h2>dddddddd</h2>
</template>
```

```
  Vue.component('mycom3',{
  	template:'#com3'
  })//将组件注册到全局
```

渲染同上

`2`私有组件

注意:私有组件只可访问组件内的data和methods

```javascript
new Vue({
  el: '#app',
  data: {},
  methods: {},
  components:{//仅当前对象的私有组件
      comp:{
          template:'<div><button @click="add">+</button><p>{{num}}</p></div>',
          data:function(){//必须是返回数据对象的函数
              return {num:0}
          },
          methods:{//组件的内部方法
            add(){
              this.num+=1
            }
          }
      }
  }
})
```

`3`组件切换

1）v-if方式切换

```html
<div id="staggered-list-demo">
  <button @click="change">更换组件</button> 
  <mycom1 v-if="flag"></mycom1> 
  <mycom2 v-if="!flag"></mycom2>   
</div>
```

```javascript
var com1=Vue.extend({
  template:'<h1>组件1</h1>'
})
Vue.component('mycom1',com1)
Vue.component('mycom2',{
  template:'<h1>组件2</h1>'
})
new Vue({
  el: '#staggered-list-demo',
  data: {
    flag:true,
  },
  methods: {
    change(){
      this.flag=!this.flag
    }
  },
})
```

2）component标签渲染切换

```html
<style>
  .v-enter,
  .v-leave-to{
    opacity: 0;
    transform: translateY(50px);
  }
  .v-enter-active,
  .v-leave-active{
    transition: all 0.5s ease;
  }
</style><!--特效设定-->
<div id="staggered-list-demo">
  <button @click="change">更换组件</button> 
  <transition mode='out-in'><!--加特效-->
		<component :is="current_comp"></component> 
  </transition>
</div>
```

```javascript
var com1=Vue.extend({
  template:'<h1>组件1</h1>'
})
Vue.component('mycom1',com1)
Vue.component('mycom2',{
  template:'<h1>组件2</h1>'
})
new Vue({
  el: '#staggered-list-demo',
  data: {
    num:0,
    current_comp:'mycom1',
    components:['mycom1','mycom2']
  },
  methods: {
    change(){
      this.num++
      this.current_comp=this.components[this.num%2]
    }
  },
})
```

`4`组件传值

1）父组件向子组件传值

```html
<div id="staggered-list-demo">
	<mycom1 :fathermsg="msg"></mycom1><!--为子组件设置属性以接收父组件的变量-->
</div>
```

```javascript
Vue.component('mycom1',{
	template:'<h1>组件1{{fathermsg}}</h1>',
    props:['fathermsg']//在子组件里注册接收传值的属性名
})
new Vue({
  el: '#staggered-list-demo',
  data: {
    msg:'msg from father',
  }
})
```

2）子组件触发绑定在自身的父组件的方法

```html
<div id="staggered-list-demo">
	<mycom1 @fnc="dofromfather"></mycom1><!--为子组件绑定自定义事件fnc（埋雷），该事件触发会执行父组件的dofromfather方法-->
</div>
```

```javascript
Vue.component('mycom1',{
  template:'<div><button @click="doit">点击调用doit方法</button></div>',
  methods:{
    doit(){
      this.$emit('fnc')//触发子组件身上绑定的自定义事件fnc（引爆地雷），如果自定义事件对应的方法有参数可以作为emit的第二个参数传过去
    }
  }
})
new Vue({
  el: '#staggered-list-demo',
  methods: {
    dofromfather(){
      console.log("this function is from father")
    }
  }
})
```

---

**ref获取dom节点**

```html
<div id="staggered-list-demo">
	<h3  ref='myh3' @click="gogogo">春残何事苦思乡，病里梳头恨最长。</h3>
</div>
```

```javascript
new Vue({
  el: '#staggered-list-demo',
  data: {},
  methods: {
    gogogo(){
      console.log(this.$refs.myh3.innerText)
    }
  }
})
```

---

**路由**

`1`思路

1）导入路由模块

2）创建组件并在app注册

3）创建路由对象，将组件设置入路由中

3）app中注册路由

`2`代码

```html
<div id="staggered-list-demo"> 
  <transition mode='out-in'><!--动画效果-->
    <router-view></router-view><!--组件会按照路由规则渲染到这里面-->
  </transition>  
  <router-link to='/login' tag='span'>登陆</router-link> 
  <router-link to='/assign' tag='span'>注册</router-link>
  <!--跳转的标签，默认渲染为a标签,这里强制改成span--> 
</div>
```

```javascript
const login={
  template:'<div>这是登陆页面</div>',
}
const assign={
  template:'<div>这是注册页面</div>',
}
const routes = [
  {path:'/',redirect:'/login'},//为匹配到则重定向
  { path: '/login', component: login },
  { path: '/assign', component: assign }
]
var router_obj=new VueRouter({
  routes,
  linkActiveClass:'active'//选中的router-link赋予class属性active
})
new Vue({
  el: '#staggered-list-demo',
  components:{//将组件注册到app中，也可以不注册，因为前面在声明路由的时候已经注册了，这里不注册就只可在路由中调用组件，不可在用<login></login>方式调用组件
    login,
    assign
  },
  router:router_obj
})
```

`3`传值

1）访问链接为"url?name=1"，获取数据`this.$route.query.name`

2）路由设置为`{path:'index/:id'}`访问链接为"index/2"时，获取数据`this.$route.params.id`

`4`路由嵌套

```html
<!--这里使用了bootstrap的css-->
<template id="bread">
  <div>
    <nav>
      <ol class="breadcrumb">
        <li class="breadcrumb-item active"><router-link to='/bread/home'>Home</router-link></li>
        <li class="breadcrumb-item active"><router-link to='/bread/library'>Library</router-link></li>
      </ol>
    </nav>
    <div class="card">
      <div class="card-body">
        <router-view></router-view>
      </div>
    </div>
  </div>
</template>
<div id="staggered-list-demo">
    <router-link to='/bread'>显示导航</router-link> 
    <router-view></router-view>
</div>
```

```javascript
const bread={//一级组件
  template:'#bread'
}
const home={//二级组件
  template:'<div>这是home页面</div>',
}
const library={//二级组件
  template:'<div>这是library页面</div>',
}
const routes = [
  {
    path:'/bread',
    component:bread,
    children:[
    { path: 'home', component: home },//这里不加'/'表示路由地址为'/bread/home',若加'/'则表示路由地址为'/home'
    { path: 'library', component: library }
    ]
  },
]
var router_obj=new VueRouter({
  routes,
})
new Vue({
  el: '#staggered-list-demo',
  router:router_obj
})
```

`5`同一路由地址下展示多个组件

```html
<div id="staggered-list-demo">
  <router-view name='comp1'></router-view>
  <router-view name='comp2'></router-view>
  <router-view name='comp3'></router-view>
</div>
```

```javascript
const comp1={
  template:'<div>组件1</div>'
}
const comp2={
  template:'<div>组件2</div>',
}
const comp3={
  template:'<div>组件3</div>',
}
const routes = [
  {
    path:'/',
    components:{//放入多个组件，通过router-view的name属性区分
      comp1,comp2,comp3
    }
    },
]
var router_obj=new VueRouter({
  routes,
})
new Vue({
  el: '#staggered-list-demo',
  router:router_obj
})
```

---

**watch的监视功能**

`1`监视文本框

本质上是监视变量，变量发生变化则会触发watch里对应的方法执行

1）变量监控

```html
<div id="staggered-list-demo">
  <input type="text" v-model='firstname'>
</div>
```

```javascript
new Vue({
  el: '#staggered-list-demo',
  data:{
    firstname:''
  },
  watch:{
    'firstname':function(newVal,oldVal){//新值 旧值
      console.log(newVal)
    }
  }
})
```

2）对象监控

```html
    <div id="staggered-list-demo">
        <input type="text" v-model="person.age">
    </div>
```

```javascript
    new Vue({
        el: '#staggered-list-demo',
        data:{
            person:{
                name:"zhanghuan",
                age:22
            }
        },
        watch:{
            person:{
                deep:true,
                handler(val){
                    //val是person对象
                    console.log(val.age)
                }
            }
        }
        })
```

`2`路由监视

```html
<div id="staggered-list-demo">
  <router-link to='/login'>login</router-link>
  <router-link to='/assign'>assign</router-link>
  <router-view></router-view>
</div>
```

```javascript
var login={
  template:'<div>this is login</div>'
}
var assign={
  template:'<div>this is assign</div>'
}
var router=new VueRouter({
  routes:[
    {path:'/login',component:login},
    {path:'/assign',component:assign}
  ]
})
new Vue({
  el: '#staggered-list-demo',
  watch:{
    '$route.path':function(newVal,oldVal){//监视路由地址，若发生变化会执行函数
      console.log(newVal)
    }
  },
  router
})
```

---

**computed存放组合计算后的变量**

```html
{% raw %}
<div id="staggered-list-demo">
  firstname:<input type="text" v-model='firstname' placeholder="firstname">
  <br>
  lastname:<input type="text" v-model='lastname' placeholder="lastname">
  <br>
  fullname:{{fullname}}<!--访问computed里的变量-->
</div>
{% endraw %}
```

```javascript
new Vue({
  el: '#staggered-list-demo',
  data:{
    'firstname':'',
    'lastname':''
  },
  computed:{
    'fullname':function(){//该变量是data中变量的计算结果，会被保存起来,通过变量名便可访问此变量
      return this.firstname+'-'+this.lastname
    }
  }
})
```

---

**nrm的使用**

nrm用于切换npm的镜像下载的地址

`1`安装

```
npm install nrm -g
```

`2`查看所有可用的镜像地址

```
nrm ls
```

`3`切换镜像地址

```
nrm use npm
```

---

**render函数渲染**

```html
<div id="app"></div>
```

```javascript
var login={
    template:'<h1>this is login</h1>'
}
var app=new Vue({
    el:'#app',
    render:function(createElements){
        return createElements(login)//这里会将login里的模板内容替换掉el指定的容器
    }
})
```

---

**wepack中使用vue**

`1`传统方式

1）安装

```
npm i vue -s
```

2）修改webpack.config.js

```javascript
const path=require('path')
const htmlwebpackplugin=require('html-webpack-plugin')//导入
module.exports={
	entry:path.join(__dirname,'./src/main.js'),
	output:{
		path:path.join(__dirname,'./dist'),
		filename:'bundle.js'
    },
    plugins:[
        new htmlwebpackplugin({
            template:path.join(__dirname,'./src/index.html'),//源页面
            filename:'index.html'//内存中的页面，将会把bundle.js注入该页面中
        })
    ],
    module:{
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']},
            {test:/\.less$/,use:['style-loader','css-loader','less-loader']},
            {test:/\.scss$/,use:['style-loader','css-loader','sass-loader']},
            {test:/\.(jpg|png|gif|bmp|jpeg)$/,use:'url-loader'},
            {test:/\.js$/,use:'babel-loader',exclude:/node_modules/}
        ]
    },
    resolve:{//新增
        alias:{
            "vue$":'vue/dist/vue.js'
        }
    }
}
```

3）main.js文件

```javascript
import Vue from 'vue'
new Vue({
    el:'#app',
    data:{
        name:'zhanghuan'
    }
})
```

`2`另一种方式

1）安装vue及.vue文件的解析工具

```
npm i vue -s
```

```
npm i vue-loader vue-template-compiler -d
```

2）修改webpack.config.js

```javascript
const path=require('path')
const htmlwebpackplugin=require('html-webpack-plugin')//导入
const VueLoaderPlugin = require('vue-loader/lib/plugin')//新增
module.exports={
	entry:path.join(__dirname,'./src/main.js'),
	output:{
		path:path.join(__dirname,'./dist'),
		filename:'bundle.js'
    },
    plugins:[
        new htmlwebpackplugin({
            template:path.join(__dirname,'./src/index.html'),//源页面
            filename:'index.html'//内存中的页面，将会把bundle.js注入该页面中
        }),
        new VueLoaderPlugin()//新增
    ],
    module:{
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']},
            {test:/\.less$/,use:['style-loader','css-loader','less-loader']},
            {test:/\.scss$/,use:['style-loader','css-loader','sass-loader']},
            {test:/\.(jpg|png|gif|bmp|jpeg)$/,use:'url-loader'},
            {test:/\.js$/,use:'babel-loader',exclude:/node_modules/},
            {test:/\.vue$/,use:'vue-loader'}//新增
        ]
    }

}
```

4）创建.vue文件

```html
<!--login.vue-->
<template>
    <h1>sdsdsd</h1>
</template> 
<script></script>
<style scoped>
/*私有样式*/
h1{
    color:red;
}
</style>
```

5）main.js文件中导入

```javascript
import login from './login.vue'
new Vue({
    el:'#app',//index.html中定义的容器
    render:c=>c(login)
})
```

6）路由

（1）安装

```
npm i vue-router -s
```

（2）导入

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'//新增
Vue.use(VueRouter)//新增
```

（3）定义vue模板

vscode上安装vetur插件

```html
<template>
<!--app.vue--> 
<!--根模板--> 
    <div>
        <router-link to='/accout'>accout</router-link>
        <router-link to='/productlist'>productlist</router-link>
        <router-view></router-view>
    </div>
</template> 
<script></script>
<style scoped>
</style>
```

```html
<template> 
<!--accout.vue--> 
    <div>
        this is accout
    </div>
</template> 
<script></script>
<style scoped>
</style>
```

```html
<template> 
<!--productlsit.vue--> 
    <div>
        this is productlist
    </div>
</template> 
<script></script>
<style scoped>
</style>
```

（4）导入模板、定义路由、创建app

```javascript
//导入vue模板
import app from './app.vue'
import accout from './accout.vue'
import productlist from './productlist.vue'

//定义路由
var router=new VueRouter({
    routes:[
        {path:'/accout',component:accout},
        {path:'/productlist',component:productlist}
    ]
})

//创建app并注册路由
new Vue({
    el:'#app',
    render:c=>c(app),
    router
})
```

> 子路由和这里类似，需要在对应vue模板定义router-view、router-link标签，router-link的to属性上按照二级路由的方式进行定义

---

**小功能案例**

回到顶部功能实现

1）定义按钮

```html
<v-btn color="primary" v-show="backTopShow" fab small dark fixed right bottom @click="$vuetify.goTo(0, 'duration')">
    <v-icon>mdi-chevron-up</v-icon>
</v-btn>
```

需要用fixed使其固定在窗口某一位置，点击后跳转到顶部

2）定义监听功能

```js
export default {
    name: 'App',
    data: () => ({
      backTopShow: true, //回到顶部按钮是否显示
    }),
    methods: {
      handleScroll() {
        let scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
        if (scrollTop > 100) {//滚动距离大于100显示，否则隐藏
          this.backTopShow = true
        }else{
          this.backTopShow = false
        }
      }
    },
    mounted() {
      window.addEventListener('scroll', this.handleScroll) // 监听滚动事件，然后用handleScroll这个方法进行相应的处理
    }
  };
```

---

**npm安装依赖报错**

原因：

在安装saas时报错，导致后面的依赖无法安装

解决办法：

 在项目内添加一个 `.npmrc` 文件 

```
phantomjs_cdnurl=http://cnpmjs.org/downloads
sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
registry=https://registry.npm.taobao.org
```

此方法不一定可以解决，可以摈弃用npm，改用yarn

Yarn安装

```
npm i -g yarn
```

安装依赖

```
yarn
```

切换国内源

```
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```

启动

```
yarn dev
```

打包

```
yarn build
```


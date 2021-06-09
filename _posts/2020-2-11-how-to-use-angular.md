---
layout: post
title: Angular
tags: angular
categories: frontend
---

Amgular安装、插件安装、新建组件、组件编写、双向绑定、服务、ViewChild、组件间通信、声明周期函数、异步编程、路由、模块化实现懒加载

**安装**

```
npm install -g @angular/cli
```

---

**查看版本**

```
ng v
```

---

**创建项目**

```
//在filedir中创建项目，并安装依赖
ng new filedir

//在filedir中创建项目，不安装依赖
ng new filedir --skip--install

//安装依赖
npm install
```

---

**SRC目录**

```
-app
	app-routing.moodule.ts
	app.component.css
	app.component.html
	app.component.spec.ts
	app.component.ts
	app.module.ts
-assets//存放静态资源
-environments
	environment.prod.ts
	enviroment.ts
favicon.ico
index.html
main.ts
polyfills.ts
test.ts
```

---

**项目中安装插件**

`1`下载

```
npm install jquery --save//以jquery为例，bootstrap同理
```

执行该命令后，会将jquery安装到node_modules中，并且会修改packge.json文件，将"jquery"作为键，版本号作为值新增到denpencies属性中

`2`将第三方库引入到项目中

打开angular.json文件，将第三方库的js路径地址新增到"scripts"属性下，将第三方库的css路径地址新增到"styles"属性下

`3`让ts文件能识别插件中的变量

```
npm install @types/jquery --save -dev//以jquery为例
```

`4`导入

```typescript
//在ts视图文件中导入
import * as $ from "jquery"
```

```css
/*css中导入*/
@import "~bootstrap/dist/css/bootstrap.css";
```

---

**新建组件**

```
ng g component components/nvbar	//在app中创建nvbar组件到components文件夹
```

执行命令后，将会在app中新建一个components文件夹，并将组件创建到该文件夹里。同时，会修改app.module.ts文件，导入该组件，并在declartions属性下字段添加新生成的组件名

---

**运行项目**

```
ng serve
```

执行该命令后项目运行

---

**组件结构**

```
-nvbar//组件文件夹
	nvbar.component.css
	nvbar.component.html	//html模板
	nvbar.component.spec.ts
	nvbar.component.ts	//ts视图
```

---

**组件编写**

`1`绑定动态属性

```html
<span [innerHTML]='content'></span><!--content是视图中声明的变量-->
```

渲染后，span标签里会填充content的值

`2`循环

```html
<ul>
	<li *ngFor="let item of arr">{ {item} }</li><!--arr是视图中定义的数组-->
</ul>
```

渲染后，这里将会迭代生成多个li标签，每个li内将填充arr的一个元素

`3`判断

```html
<p>
    <span *ngIf="flag">flag is true</span><!--视图中flag值为true是渲染这个标签-->
    <span *ngIf="!flag">flag is false</span><!--视图中flag值为false是渲染这个标签-->
</p>
```

`4`switch...case...

```html
<div [ngSwitch]="status">
    <div *ngSwitchCase=1>status's value is 1</div><!--视图中status的值是1，渲染这个标签-->
    <div *ngSwitchCase=2>status's value is 2</div>
    <div *ngSwitchCase=3>status's value is 3</div>
    <div *ngSwitchDefault>status's value is 0</div>
</div>
```

`5`动态样式

```html
<div [ngClass]="{'orange':true,'red':false}">www.zhanghuan.top</div>
<!--预先在css文件中定义两个class，orange和red，这里会将传入对象中属性值为true的属性作为class添加到该标签中-->
```

```html
<div [ngStyle]="{'color': 'red'}">www.zhanghuan.top</div>
<!--这里直接改变该标签的style属性，此处为设置div的color为red-->
```

`6`管道

```html
<div>{ {time|date:'yyyy-MM-dd hh:mm:ss'} }</div>
<!--视图文件中time:any=new Date()-->
```

`7`事件

1)点击事件

```html
<!--点击事件-模板-->
<button (click)='run($event)'>执行事件</button><!--注意：要捕捉该事件，这里的参数必须是$event--> 
```

点击该按钮后将会调用视图中的run方法，$event是捕捉该事件并传入run中。视图里需要定义run方法，并接收传过来的参数，event.target将指向该按钮对象

2）按钮事件

```html
<!--按钮事件-模板-->
<input type="text" (keydown)="keydown($event)">
```

```typescript
//视图中部分代码
keydown(e:any){
     if(e.keyCode==13){//如果点击回车，输出input里的值
      console.log(e.target.value)
     }    
}
```

3）双向数据绑定

(1)需要配置app.modules.ts

```typescript
import {FormsModule} from '@angular/forms';//导入该模块

@NgModule({
    imports: [
        BrowserModule,
        AppRoutingModule,
        FormsModule//这里新增模块名
      ]
})
```

(2)具体代码

```html
<!--双向绑定-模板-->
<input type="text" [(ngModel)]="keywords"/>{{keywords}}
```

输入文本框的内容将会赋值给keywords这个变量，keywords的值随着输入内容的改变而改变。注意：视图函数中如果不声明keywords这个变量，也不会报错

---

**表单中双向绑定案例**

1.模板

```html
<div class="container">
    <h1 class="text-center">人员登记系统</h1>
    <div class="card">
        <div class="card-body">
            <form>
                <!--文本框-->
                <div class="form-group">
                    <label for="name">姓名</label>
                    <input type="text" class="form-control" [(ngModel)]="peopleinfo.name" name="name" id="name"><!--注意：不设置name属性会报错-->
                </div>

                <!--单选框-->
                <p>性别</p>
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="radio" id="sex0" [(ngModel)]="peopleinfo.sex" name="sex" value="0">
                    <label class="form-check-label" for="sex0">男</label>
                </div>
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="radio" id="sex1" [(ngModel)]="peopleinfo.sex" name="sex" value="1">
                    <label class="form-check-label" for="sex1">女</label>
                </div>
                <p></p>

                <!--下拉菜单-->
                <p>城市</p>
                <select class="form-control" name="city" [(ngModel)]="peopleinfo.city">
                    <option *ngFor="let item of citylist">{ {item} }</option>
                </select>
                <p></p>

                <!--复选框-->
                <p>爱好</p>
                <div class="form-check form-check-inline" *ngFor="let item of peopleinfo.hobby;let key=index">
                    <input class="form-check-input" type="checkbox" name="hobby" [id]="'hobby'+key" [(ngModel)]="item.checked">
                    <label class="form-check-label" [for]="'hobby'+key">{ {item.title} }</label>
                </div>
                <p></p>

                <!--大文本框-->
                <div class="mb-3">
                    <label for="validationTextarea">备注</label>
                    <textarea class="form-control" id="validationTextarea" name="mark" required [(ngModel)]="peopleinfo.mark"></textarea>
                </div>
            </form>
        </div>
    </div>
</div>
{ {peopleinfo|json} }<!--以json形式展示对象-->
```

2.视图中关键代码

```typescript
  public citylist:any[]=["上海","北京","广州","深圳"]
  public peopleinfo:any={
    "name":"",
    "sex":"0",
    "city":"上海",
    "hobby":[
      {
        "title":"吃饭",
        "checked":false
      },
      {
        "title":"睡觉",
        "checked":false
      },
      {
        "title":"敲代码",
        "checked":false
      }
    ],
    "mark":""
  }
```

---

**搜索历史案例**

1.模板

```html
<div class="container">
    <div class="card">
        <div class="card-body">
            <form class="form-inline">
                <div class="form-group col-sm-9 col-xs-9 mx-sm-3 mb-2">
                  <label for="search" class="sr-only">search</label>
                  <input type="text" class="form-control col-sm-12" name="keywords" (keydown)="getkey($event)" [(ngModel)]="keywords" id="search">
                </div>
                <button type="submit" (click)="add_history()" class="btn btn-primary mb-2">搜索</button>
              </form>
              <hr>
              
              <!--循环search_his-->
              <h3 *ngFor="let item of search_his"><span class="badge badge-light large">
                  { {item} }
                <button type="button" class="close" (click)="del(item)">
                  <span>&times;</span>
                </button>
              </span></h3> 
        </div>
    </div>
</div>
```

2.视图中关键代码

```typescript
search_his:any[]=[]
  keywords:string=""
  getkey(e:any){
    //监听用户键盘事件，如果输入回车即向搜索历史中添加文本框中的关键字
    if(e.keyCode==13){
      this.add_history()
    }
  }
  add_history(){
    //向搜索历史search_his中添加文本框中的关键字
    if(this.search_his.indexOf(this.keywords)==-1){//如果search_his中没有当前搜索的数据，则进行添加
      this.search_his.push(this.keywords)
    }
  }
  del(item:any){
    //从搜索历史search_his中删除item关键字
    this.search_his.splice(this.search_his.indexOf(item),1)
  }
```

---

**任务列表案例**

1.模板

```html
<div class="container">
    <div class="card">
      <div class="card-header"><h3 class="text-center">toDoList</h3></div>
        <div class="card-body">
            <form>
                <div class="form-group ">
                  <label for="search" class="sr-only">search</label>
                  <input type="text" class="form-control col-sm-12" placeholder="输入后点击回车添加新任务" name="keywords" (keydown)="getkey($event)" [(ngModel)]="keywords" id="search">
                </div>
              </form>
              <hr>
              <p>进行中任务</p>
              <div class="custom-control custom-checkbox" *ngFor="let item of task_list;let key=index" [hidden]="item.status==true">
                <input type="checkbox" class="custom-control-input" [(ngModel)]="item.status" [id]="'task'+key">
                <label class="custom-control-label" [for]="'task'+key">{ {item.name} }</label>
                <button type="button" class="close" (click)="del(index)">
                  <span>&times;</span>
                </button>
              </div>
              <hr>
              <p>已完成任务</p>
              <div class="custom-control custom-checkbox" *ngFor="let item of task_list;let key=index" [hidden]="item.status==false">
                <input type="checkbox" class="custom-control-input" [(ngModel)]="item.status" [id]="'task'+key">
                <label class="custom-control-label" [for]="'task'+key">{ {item.name} }</label>
                <button type="button" class="close" (click)="del(index)">
                  <span>&times;</span>
                </button>
              </div>
        </div>
    </div>
</div>
```

2.视图中关键代码

```typescript
task_list:any[]=[]
  keywords:string=""
  getkey(e:any){
    //监听用户键盘事件，如果输入回车即向搜索历史中添加文本框中的关键字
    if(e.keyCode==13){
      this.add_task()
      e.target.value=""
    }
  }
  add_task(){
    //向任务列表中添加文本框中的关键字
    if(this.judge_task_in_list(this.keywords)){//如果任务列表中不存在该关键词，则进行添加
      this.task_list.push({"name":this.keywords,"status":false})
    }
  }
  judge_task_in_list(key:any){
    //判断任务列表中是否已存在传入的关键词，不存在返回true
    for(var i=0;i<this.task_list.length;i++){
      if(this.task_list[i].name==key){
        return false
      }
    }
    return true
  }
  del(index:any){
    //从任务列表中删除item关键字
    this.task_list.splice(index,1)
  }
```

---

**服务**

Angular中公共资源的一种存在形式

`1`新建服务

```
ng g service services/storage	//在app中创建storage服务到services文件夹
```

`2`app中配置服务

修改app.modules.ts文件

```typescript
//导入服务
import {StorageService} from './services/storage.service'

//添加服务的类名到providers属性
providers: [
    StorageService
  ],
```

`3`视图中使用服务

```typescript
//导入服务
import {StorageService} from '.././services/storage.service';

//组件类的构造方法传入服务
constructor(public storage:StorageService){
    //此时该类已自动新增属性storage，用this.storage可调用服务
  }
```

---

**任务列表消息持久化升级案例**

1.模板

```html
<div class="container">
    <div class="card">
      <div class="card-header"><h3 class="text-center">toDoList</h3></div>
        <div class="card-body">
            <form>
                <div class="form-group ">
                  <label for="search" class="sr-only">search</label>
                  <input type="text" class="form-control col-sm-12" placeholder="输入后点击回车添加新任务" name="keywords" (keydown)="getkey($event)" [(ngModel)]="keywords" id="search">
                </div>
              </form>
              <hr>
              <p>进行中任务</p>
              <div class="custom-control custom-checkbox" *ngFor="let item of task_list;let key=index" [hidden]="item.status==true">
                <input type="checkbox" class="custom-control-input" [(ngModel)]="item.status" (change)="update_to_local()" [id]="'task'+key"><!--较之前新增change事件-->
                <label class="custom-control-label" [for]="'task'+key">{ {item.name} }</label>
                <button type="button" class="close" (click)="del(index)">
                  <span>&times;</span>
                </button>
              </div>
              <hr>
              <p>已完成任务</p>
              <div class="custom-control custom-checkbox" *ngFor="let item of task_list;let key=index" [hidden]="item.status==false">
                <input type="checkbox" class="custom-control-input" [(ngModel)]="item.status" [id]="'task'+key">
                <label class="custom-control-label" [for]="'task'+key">{ {item.name} }</label>
                <button type="button" class="close" (click)="del(index)">
                  <span>&times;</span>
                </button>
              </div>
        </div>
    </div>
</div>
```

2.视图中完整代码

```typescript
import { Component, OnInit } from '@angular/core';
import {StorageService} from '.././services/storage.service';

@Component({
  selector: 'app-nvbar',
  templateUrl: './nvbar.component.html',
  styleUrls: ['./nvbar.component.css']
})
export class NvbarComponent implements OnInit {
 
  task_list:any[]=[]
  keywords:string=""
  constructor(public storage:StorageService){
    //此时该类已自动新增属性storage，用this.storage可调用服务
  }
  getkey(e:any){
    //监听用户键盘事件，如果输入回车即向搜索历史中添加文本框中的关键字
    if(e.keyCode==13){
      this.add_task()
      e.target.value=""
    }
  }
  add_task(){
    //向任务列表中添加文本框中的关键字
    if(this.judge_task_in_list(this.keywords)){//如果任务列表中不存在该关键词，则进行添加
      this.task_list.push({"name":this.keywords,"status":false})
      this.update_to_local()
    }
  }
  judge_task_in_list(key:any){
    //判断任务列表中是否已存在传入的关键词，不存在返回true
    for(var i=0;i<this.task_list.length;i++){
      if(this.task_list[i].name==key){
        return false
      }
    }
    return true
  }
  del(index:any){
    //从任务列表中删除item关键字
    this.task_list.splice(index,1)
    this.update_to_local()
  }
  update_to_local(){
    //将任务列表更新到本地
    this.storage.set("todolist",this.task_list)
  }
  ngOnInit() {
    //当页面刷新时，会执行此方法
    this.task_list=this.storage.get("todolist")
  }

}
```

---

**ViewChild获取DOM节点**

`1`视图里引入ViewChild

```typescript
import { ViewChild } from '@angular/core';
```

`2`模板中的标签绑定上id

```html
<p #ibox>box</p><!--必须以此种方式写id，否则无法识别-->
```

`3`视图中装饰类里的属性

```typescript
@ViewChild('ibox',{static: false}) mybox:any
```

`4`在视图的ngAfterViewInit函数中调用DOM节点

```typescript
ngAfterViewInit():void{
    //$("#box").css("color","red")
    $(this.mybox.nativeElement).css("color","red")
  }
```

`5`侧边栏隐藏案例

```css
//style.css文件
body{
    overflow: hidden;
}
```

```css
//aside.component.css文件
#right{
    background: black;
    width:200px;
    height:100%;
    right:0;
    top:0;
    position: absolute;
    transform: translate(0,0);
    transition: all 0.5s;
}
```

```html
<!--aside.component.html文件-->
<aside id="right" #right></aside>
<button class="btn btn-info" (click)="showaside()">弹出</button>
<button class="btn btn-info" (click)="hideaside()">隐藏</button>
```

```typescript
//aside.component.ts视图中关键代码
import { ViewChild } from '@angular/core';

@ViewChild('right',{static:false}) aside:any
  ngOnInit() {
    //当页面刷新时，会执行此方法

  }
  showaside(){
    $(this.aside.nativeElement).css("transform","translate(0,0)")
  }
  hideaside(){
    $(this.aside.nativeElement).css("transform","translate(100%,0)")
  }
```

---

**父组件与子组件间的通信**

`1`父组件发消息给子组件

子组件视图

```typescript
import { OnInit } from '@angular/core';
export class AsideComponent implements OnInit {
    @Input() from_father:any//定义变量，从父组件接收数据
}
```

父组件模板

```typescript
<app-aside [from_father]="the msg from father""></app-aside>
//父组件在模板通过定义动态属性，向子组件发送数据
```

`2`子组件发消息给父组件

子组件视图

```typescript
import { Output,EventEmitter } from '@angular/core';
export class AsideComponent implements OnInit {
    @Output() private outer=new EventEmitter()//定义消息发送器，将通过outer对象向父组件发送数据
    ngOnInit() {
  		this.outer.emit("我是子组件")//组件初始化完成后发送数据给父组件  
  	}

}
```

父组件模板

```html
<app-aside (outer)="run($event)"></app-aside>
<!--这里必须用outer事件(和子组件变量outer名命保持一致)触发视图内的方法来接收子组件传过来的数据-->
```

---

**生命周期函数**

组件创建、更新、销毁时触发的一些列方法

---

**ngOnInit和ngAfterViewInit的区别**

```typescript
  ngOnInit() {
    //组件和指令初始化完成执行，并不是真正的dom加载完成,请求数据操作放在这里
  }
  ngAfterViewInit():void{
    //视图加载完成后触发的方法，dom操作放在这里
  }
```

---

**异步编程的几种方法**

`1`回调函数

`2`事件监听、发布订阅

`3`Promise

`4`Rxjs，本质上就是第2中方法

example:

通过一个服务，模拟向服务端请求数据的异步过程

1)服务的核心ts代码

```typescript
import {Observable} from 'rxjs'//仅用于rxjs

export class RequestService {
//服务端请求数据后，这里通过定时器延长数据发送时间，模拟数据请求延迟
  data:any=""
  constructor() { }
  
  //定义回调函数异步
  get_callback_data(cb){
    var t=setInterval(()=>{
      this.data='data from callback'
      window.clearInterval(t)//此句注释会持续执行回调函数，即每隔3秒将会发送一次信息
      cb(this.data)
    },3000)
    
  }
    
  //定义promise异步
  get_promise_data(){
    return new Promise((resolve)=>{
      var t=setInterval(()=>{
        this.data='data from promise'
        window.clearInterval(t)//此句注释后效果一样，即仅发送一次信息
        resolve(this.data)
      },3000)
    })
  }
  
  //定义rxjs异步
  get_rxjs_data(){
    return new Observable((observe)=>{
      var t=setInterval(()=>{
        this.data='data from rxjs'
        observe.next(this.data)//每隔3秒将会发送一次信息
        //observe.error("失败时返回数据")
      },3000)
    })
  }
    
}
```

2)组件模拟客户端核心ts代码

```typescript
export class AsideComponent implements OnInit {
    ngOnInit() {
        //回调函数方式
        this.request.get_callback_data((data)=>{
          console.log(data)//data from callback
        })
  		
        //promise方式
        this.request.get_promise_data().then((data)=>{
          console.log(data)//data from promise
        })
        
        //rxjs方式
        var d=this.request.get_rxjs_data().subscribe((data)=>{
          console.log(data)
        })
        //取消订阅,将中途撤回消息获取
        //d.unsubscribe()
  }
 }
```

---

**Rxjs异步总结**

功能较promise强大，获取消息可中途撤回，获取的消息可以进行过滤

```typescript
//消息过滤功能演示
//get_rxjs_data()将会持续发送0，1，2，3，4……
//这是ts视图模拟客户端获取数据
import { filter,map } from 'rxjs/operators'
export class AsideComponent implements OnInit {
    this.request.get_rxjs_data().pipe(
      filter((value:any)=>{if(value%2==0){return true;}})
    ).subscribe((data)=>{
      console.log(data)//输出0，2，4，6……
    })
}
```

---

**请求数据**

`1`配置app.modules.ts

```typescript
//app.modules.ts文件

//导入
import { HttpClientModule } from '@angular/common/http'
@NgModule({
  declarations: [
    AppComponent,
    HomeComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule//新增模块
  ],
  providers: [],
  bootstrap: [AppComponent]
})
```

`2`视图函数中使用(无参数的get请求)

```typescript
//导入模块
import { HttpClient } from '@angular/common/http'

export class HomeComponent implements OnInit {

  constructor(public http:HttpClient) { }//通过构造函数将新导入模块传入并赋值给http

  ngOnInit() {
  }
    
	//调用该方法将会发出请求
  getData(){
    let api="http://a.itying.com/api/productlist"
    this.http.get(api).subscribe((response)=>{
      console.log(response)//输出请求的数据
    })
  }

}
```

`3`带参数的post请求

```typescript
import { HttpClient } from '@angular/common/http'
import { HttpHeaders } from '@angular/common/http'//请求头模块

export class HomeComponent implements OnInit {

  constructor(public http:HttpClient) { }

  ngOnInit() {
  }
    
  dologin(){
    const httpoptions={
      headers:new HttpHeaders({'ContentType':"aplication/json"})//设置请求头
    }
    let api="121.0.0.1:3000/login"
    this.http.post(api,{"username":"zhanghuan","password":"123456"},httpoptions).subscribe((response)=>{
      console.log(response)
    })
  }

}
```

`4`使用jsonp跨域

1)在app.module.ts里把jsonp模块导入并配置

```typescript
import { HttpClientModule } from '@angular/common/http'
import { HttpClientJsonpModule } from '@angular/common/http'
@NgModule({
  declarations: [
    AppComponent,
    HomeComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,//新增模块
    HttpClientJsonpModule//新增模块
  ],
  providers: [],
  bootstrap: [AppComponent]
})
```

2)在视图文件中使用

```typescript
//关键代码
get_jsonp_data(){
    let api="https://sug.so.360.cn/suggest?"
    this.http.jsonp(api,"callback").subscribe((response)=>{
      console.log(response)
    })
  }
```

`5`axios获取数据

angular默认安装了此模块，如果未安装执行`npmn install axios --save`

```typescript
//关键代码
import axios from 'axios'

get_axiod_data(){
    let api="http://a.itying.com/api/productlist"
    axios.get(api).then((response)=>{
      console.log(response)
    })
  }
```

---

**路由**

路由就是根据不同的url地址，动态让根组件挂载其他组件来实现一个单页面应用

`1`配置路由

```typescript
//app-routing.module.ts
//关键代码
const routes: Routes = [
  {path:"home",component:HomeComponent},
  {path:'content',component:ContentComponent},
  {path:'**',component:HomeComponent},//这个必须放最下面，其下面的路由都匹配不到
];
```

`2`模板

```html
<a href="" [routerLink]="['/home']" routerLinkActive="text-info" >首页</a>
<a href="" [routerLink]="['/content']" routerLinkActive="text-info" >详情</a>
<a href="" [routerLink]="['/about']" routerLinkActive="text-info" >关于</a>
<!--以上a标签点击后不会刷新网页，routerLinkActive属性为点击后给该标签加上对应的text-info类，移除其他标签的text-info类-->
<a href="/about">关于</a><!--这是传统a标签会刷新网页-->
```

`3`a标签进行get方式传值

```html
<!--模板-->
<a href="" [routerLink]="['/about']" routerLinkActive="text-info" [queryParams]="{name:'zhanghuan'}">关于</a>
<!--queryParams动态属性赋值参数对象-->
```

```typescript
//about对应的视图
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router'//导入

@Component({
  selector: 'app-about',
  templateUrl: './about.component.html',
  styleUrls: ['./about.component.scss']
})
export class AboutComponent implements OnInit {

  constructor(public route:ActivatedRoute) { }

  ngOnInit() {
    this.route.queryParams.subscribe((data)=>{
      console.log(data)//输出 {name: "zhanghuan"}
    })
  }

}
```

`4`动态路由

1)路由

```typescript
//路由
const routes: Routes = [
  {path:'content/:aid',component:ContentComponent},//aid接收参数
];
```
2)模板

```html
<!--模板-->
<a href="" [routerLink]="['/content',1]" routerLinkActive="text-info" >详情</a>
```
3)views文件

```typescript
//content的views文件
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router'//导入

@Component({
  selector: 'app-content',
  templateUrl: './content.component.html',
  styleUrls: ['./content.component.scss']
})
export class ContentComponent implements OnInit {

  constructor(public router:ActivatedRoute) { }

  ngOnInit() {
    this.router.params.subscribe((data)=>{
      console.log(data)//输出 {aid: "1"}
    })
  }

}
```

`5`js跳转

1)路由

```typescript
const routes: Routes = [
  {path:"home",component:HomeComponent},
  {path:'content/:aid',component:ContentComponent},
  {path:'about',component:AboutComponent},
  {path:'**',redirectTo:"home"},//这个必须放最下面，其下面的路由都匹配不到
];
```

2)模板

```html
<button class="btn btn-info" (click)="skip_home()">js方式跳转home</button>
<button class="btn btn-info" (click)="skip_content()">js方式跳转content</button>
<button class="btn btn-info" (click)="skip_about()">js方式跳转about</button>
```

3)views文件

```typescript
import { Component } from '@angular/core';
import { Router } from '@angular/router'//导入
import { NavigationExtras } from '@angular/router'//导入

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'angulardemo02';
  constructor(public router:Router){}
  skip_home(){//简单跳转
    this.router.navigate(['home'])
  }
  skip_content(){//动态路由传值跳转
    this.router.navigate(['content',1])
  }
  skip_about(){//get方式跳转传值
    let queryParams:NavigationExtras={
      queryParams:{"name":"zhanghuan"}
    }
    this.router.navigate(['about'],queryParams)
  }
}
```

`6`二级路由

1)路由文件

```typescript
const routes: Routes = [
  {path:'home',component:HomeComponent},
  {path:'product',component:ProductComponent,
    children:[
      {path:'class_manage',component:ClassifiedManagementComponent},
      {path:'commodity',component:CommondityManagementComponent},
      {path:'**',redirectTo:"class_manage"}
    ]
  },
  {path:'**',redirectTo:"home"}
];
```

2)product组件的视图核心代码

```typescript
export class ProductComponent implements OnInit {
  mlist:any[]=[
    {"title":"分类管理","url":"class_manage"},
    {"title":"商品列表","url":"commodity"}
  ]
  constructor() { }

  ngOnInit() {
  }

}
```

3)product组件的模板

```html
<div class="container-fluid">
    <div class="row">
      <div class="col-md-3"  style="height: 100%;">
  
        <div class="card" >
          <div class="card-body">
            <div class="list-group" *ngFor="let item of mlist">
              <a class="list-group-item list-group-item-action" [routerLink]="[item.url]" routerLinkActive="active" >{ {item.title} }</a>
            </div>
            <p></p>
          </div>
        </div>
  
      </div>
      <!-- 左侧导航 -->
  
      <div class="col-md-9">
        <div class="card">
          <div class="card-body">
            <router-outlet></router-outlet>
            <!-- 二级路由对应的组件将渲染在router-outlet里 -->
          </div>
        </div>
      </div>
      <!-- 右侧主体 -->
    </div>
  </div>
```

---

**模块化实现懒加载**

组件较多导致加载慢，将组件模块化使得初始化更快

`1`创建模块

```
ng g module modules/user --routing
```

将会创建一个modules文件夹，里面包含一个user文件夹，此文件夹内包含两个文件，分别为user-routing.module.ts，user.module.ts

`2`创建根组件

```
ng g component modules/user
```

将会在modules/user里创建四个文件，分别是user.component.ts，user.component.spec.ts，user.component.html，user.component.css

`3`通过路由跳转到对应模块

1）一级路由设置

```typescript
//app-routing.module.ts核心代码
const routes: Routes = [
  {path:'user',loadChildren:'./modules/user/user.module#UserModule'},
  {path:'product',loadChildren:'./modules/product/product.module#ProductModule'},
  {path:'**',redirectTo:'user'},
];
```

2）二级路由设置

```typescript
//product-routing.module.ts核心代码
const routes: Routes = [
  {path:"",component:ProductComponent}
];
```

`4`在模块外部访问模块内的组件

1）配置模块的module.ts文件

```typescript
//模块的module.ts文件，如user.module.ts，这里仅仅是核心代码
@NgModule({
  declarations: [UserComponent, ProfileComponent, AdressComponent, OrderComponent],
  exports:[AdressComponent],//将想要外部访问的组件进行暴露
  imports: [
    CommonModule,
    UserRoutingModule
  ]
})
```

2）在app.module.ts文件中进行配置

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppRoutingModule } from './app-routing.module';
import { UserModule } from './modules/user/user.module';//外部模块导入
import { ProductModule } from './modules/product/product.module';//外部模块导入
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    ProductModule,//外部模块导入
    UserModule,//外部模块导入
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```


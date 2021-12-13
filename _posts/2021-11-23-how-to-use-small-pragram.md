---
layout: post
title: 微信小程序
tags: front
categories: front
---

基础知识、数据、样式、标签、组件、API、实现案例



**基础知识**

1）目录结构

```
# 存放页面
- pages
# 存放工具
- utils
- app.js
- app.json
- app.wxss
- project.config.json
- sitemap.json
```

2）全局配置文件app.json

```json
 "pages": [
    "pages/zhanghuan/zhanghuan",#页面，新增后自动创建页面文件
  ],
  "window": {
    "backgroundColor": "#F6F6F6",#下拉背景色
    "backgroundTextStyle": "light",#下拉loading样式
    "navigationBarBackgroundColor": "#F6F6F6",#导航背景色
    "navigationBarTitleText": "云开发 QuickStart",#标题
    "navigationBarTextStyle": "black"#导航文字颜色
  },
  "sitemapLocation": "sitemap.json",
  "style": "v2"
}
```

3）底部导航

```json
{
  "pages": [
    "pages/camera/camera",#定义页面
    "pages/me/me",#定义页面
  ],
  "window": {
    "backgroundColor": "#F6F6F6",
    "backgroundTextStyle": "light",
    "navigationBarBackgroundColor": "#F6F6F6",
    "navigationBarTitleText": "云开发 QuickStart",
    "navigationBarTextStyle": "black"
  },
  "sitemapLocation": "sitemap.json",
  "style": "v2",
  "tabBar": {
    "list": [{
      "pagePath": "pages/camera/camera",#指定页面
      "text": "camera",
      "iconPath": "icons/camera.png",#指定图标
      "selectedIconPath": "icons/camera1.png"#选中时图标
    },{
      "pagePath": "pages/me/me",
      "text": "me",
      "iconPath": "icons/me.png",
      "selectedIconPath": "icons/me1.png"
    }]
  }
}
```

4）页面配置文件

即pages目录下子目录中.json文件

```
{
  "usingComponents": {},
  "navigationBarBackgroundColor": "#000000",
  "navigationBarTextStyle": "white"
}
```

5）常用标签

```html
<!--相当于span标签-->
<text>11</text>
<!--相当于div标签-->
<view>ss</view>
<!--多选框-->
<checkbox checked="{{false}}"></checkbox>
<!--占位符标签-->
<block></block>
```

---

**数据**

1）数据绑定

```js
Page({
    data:{
        msg:"111",
        num:1111,
        isTrue:false，
        person:{
        	age:22
        	name::"sss"
    	}
    }
})
```

```html
<view>{{msg}}</view>
<view>{{num}}</view>
<view>{{isTrue}}</view>
<view>{{person.age}}</view>
<checkbox checked="{{isTrue}}"></checkbox><!--主要不要有空格-->
```

2）运算

```html
<view>{{1+1}}</view><!--2-->
<view>{{"1"+"1"}}</view><!--11-->
<view>{{10%2==0?"偶数":"奇数"}}</view><!--偶数-->
```

3）循环

```html
<view wx:for="{{list}}" wx:for-item="item" wx-for-index="index" wx:key="id">
{{index}}{{item.name}}
</view>
```

wx:key 绑定一个普通字符串时，这个字符串名称一定是循环数组中的对象的唯一属性

wx:key="*this"中的\*this表示的是循环项

4）判断

```html
<view wx:if="{{true}}">显示</view>
<!--不要和display一起用-->
<view hidden>aaa</view>
```

常用于显示隐藏，过于频繁用hidden属性

5）样式绑定

```html
<input type="text" bindinput="handleInput"/>
<view>{{num}}</view>
<button bindtap="add" data-operation="{{1}}">+</button>
<button bindtap="add" data-operation="{{-1}}">-</button>
```

```js
Page({
    data:{
        num:1111,
    },
    handleInput(e){
        this.setData({
            num:e.detail.value
        })
    },
    add(e){
        const operation = e.currentTarget.dataset.operation
        this.setData({
            num:this.data.num+operation
        })
    }
})
```

---

**样式**

1）快速上手

```html
<view></view>
```

```
view{
  width:200rpx;
  height:200rpx;
  background-color: red;
}
```

2）计算

```
view{
  width:100rpx;
  height:calc(100*750rpx/375);
  background-color: red;
}
```

3）引用

```
@import "../../styles/test.wxss"
/*只能写相对路径*/
```

4）vscode编译less

安装小程序助手、easyless，并配置vscode

```
{
    "less.compile": {
        "outExt": ".wxss"
    }
}
```

编写好less后会自动将.less编译成.wxss

定义一个common.less的文件

```less
@themecolor:red;
```

导入

```less

@import "../../less_style/common.less";

view{
    color:@themecolor;
}
```

---

**标签**

1）image

```html
<!--
mode
scollToFill 默认值，不保持纵横比缩放图片
aspectFit 保持宽高比，确保图片长边显示出来 轮播图常用
aspectFill 保持纵横比缩放图片，只保证图片的短边显示出来，少用
widthFix 宽度指定后，高度会按比例来调整，常用
bottom... 类似以前的backgroud-position

lazy-load
当图片出现在视口上下三屏高度之内时，自动加载图片
-->
<image mode="widthFix" src="https://p3.img.cctvpic.com/photoworkspace/2021/11/05/2021110521093859472.jpg"></image>
```

2）swiper

```html
<!--
swiper标签默认样式
width 100%
height 150px
swiper高度无法实现由内容撑开

autoplay 自动轮播
interval 修改轮播时间
circular 轮播
indicator-dots 显示分页器
indicator-color 治时期的未选择颜色
indicator-active-color 选中的时候的指示器的颜色
-->

<swiper autoplay circular indicator-dots interval="1000">
    <swiper-item>
        <image mode="widthFix" src="https://dgss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=1709127955,157333981&fm=30&app=106&f=JPEG?w=312&h=208&s=383760945DDC0C534CD3758F0300E088"></image>
    </swiper-item>
    <swiper-item>
        <image mode="widthFix" src="https://p3.img.cctvpic.com/photoworkspace/2021/11/05/2021110521093859472.jpg"></image>
    </swiper-item>
    <swiper-item>
        <image mode="widthFix" src="https://p1.img.cctvpic.com/photoworkspace/2021/11/05/2021110520301275038.jpg"></image>
    </swiper-item>
</swiper>  
```

3）navigator

```html
<!--
url 要跳转到的页面路径，不可跳转到外网
target 要跳转到当前的小程序
  self 自己 小程序页面
  miniProgram 其他的小程序页面
open-type 跳转方式
  navigate 默认值，保留当前页面，跳转到应用内的某个页面，但是不能跳到tabbar页面
  redirect 关闭当前页面，跳转到某个页面，单不能跳到tabbar页面
  switchTab 跳转到tabBar页面，并关闭其他非tabBar页面
  reLaunch 关闭所有页面，打开到应用内的某个页面
  navigateBack 关闭当前页面，返回上一页或多级页面
  exit 退出小程序
-->
<navigator url="/pages/test1/7">7</navigator>
```

4）button

```html
<!--
button标签
size:
  default 默认大小
  mini 小
type:
  default 灰色
  primary 绿色
  warn 红色
-->
<button>默认按钮</button>
<button type="primary">默认按钮</button>
<button type="warn" plain>默认按钮</button>
<button type="warn" loading>默认按钮</button>
```

```html
<!--
open-type:
   contact 客户对话功能
   share 转发当前的小程序 到微信朋友中，不能把小程序分享到朋友圈
   getPhoneNumber 获取当前用户的手机号码信息，结合一个事件来用，不是企业小程序账号，没有权限来获取用户的号码
     1 绑定一个事件 bindgetphonenumber
     2 在事件的回调函数中，通过参数来获取信息
     3 获取到的信息，已加密，需在后台进行解析
   getUserInfo 获取当前用户的个人信息
   launchApp 在小程序中直接打开app
   openSetting 打开小程序内置的 授权页面
   feedback 打开小程序内置的一件反馈页面
-->
<button type="primary" open-type="contact">contact</button>
<button type="primary" open-type="share">share</button>
<button type="primary" open-type="getPhoneNumber" bindgetphonenumber="getPhoneNumber">getPhoneNumber</button>
<button type="primary" open-type="getUserInfo" bindgetuserinfo="getIserInfo">getUserInfo</button>
<button type="primary" open-type="launchApp">launchApp</button>
<button type="primary" open-type="openSetting">openSetting</button>
<button type="primary" open-type="feedback">feedback</button>
```

contact 实现流程

1.绑定appid

2.登录小程序官网，添加客服-微信

5）icon

```html
<!--
type 图标类型
  success succeess_no_circle info warn waiting cancel download search clear
size 大小
color 图标颜色
-->

<icon type="success" size="60" color="primary"></icon>
```

6）radio

```html
<!--
  radio 单选框
  1 radio必须和父元素 radio=group配合使用
  2 value 选中的单选框的值
  3 需要给 radio-group 棒球change 事件
-->

<radio-group bindchange="handlerChange">
  <radio value="male">男</radio>
  <radio value="female">女</radio>
</radio-group>

<view>您选中的是：{{gender}}</view>
```

```js
Page({
  data: {
    gender:""
  },
  handlerChange(e){
    this.setData({
      gender:e.detail.value
    })
  }
})
```

7）checkbox

```html
<radio-group bindchange="handlerChange">
  <radio value="male">男</radio>
  <radio value="female">女</radio>
</radio-group>
<checkbox-group bindchange="handleCheck">
  <checkbox value="{{item.value}}" wx:for="{{list}}" wx:key="id">{{item.value}}</checkbox>
</checkbox-group>

<view>您选中的是：{{checkedlist}}</view>
```

```js
Page({
  data: {
    list:[
      {
        id:1,
        name:"篮球",
        value:"basketball"
      },
      {
        id:2,
        name:"足球",
        value:"football"
      }
    ],
    checkedlist:[]
  },
  handleCheck(e){
    this.setData({
      checkedlist:e.detail.value
    })
  }
})
```

---

**组件**

1）基础流程

(1)创建组件

在pages的同级目录创建components文件，里面创建一个tabs文件夹，在该文件夹内创建4个组件文件

(2)在页面的json文件中引用组件

```json
{
  "usingComponents": {
    "tabs":"../../components/tabs/tabs"
  }
}
```

(3)在wxml中使用组件

```html
<tabs></tabs>
```

2）导航案例

```html
<view>
  <view class="tabs_title">
    <view 
    bindtap="handleTab"
    data-index="{{index}}"
    wx:for="{{tabs}}" 
    wx:key="id"
    class="title_item {{item.isActive?'active':''}}">
    {{item.name}}
    </view>
  </view>
  <view class="tabs_content" >内容</view>
</view>
```

```css
.tabs_title{
    display: flex;
    padding:10rpx;
}
.title_item{
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
}
.active{
    color:red;
    border-bottom: 10rpx solid currentColor;
}
```

```js
// components/tabs/tabs.js
Component({
  /**
   * 组件的属性列表
   */
  properties: {

  },

  /**
   * 组件的初始数据
   */
  data: {
    tabs:[
      {
        id:1,
        name:"首页",
        isActive: true
      },
      {
        id:2,
        name:"原创",
        isActive: false
      },
      {
        id:3,
        name:"分类",
        isActive: false
      },
      {
        id:4,
        name:"关于",
        isActive: false
      }
    ]
  },
  /**
   * 1 页面.js 事件的回调函数放在data同层级下
   * 2 组件.js 事件的回调函数放在method中
   */

  /**
   * 组件的方法列表
   */
  methods: {
    handleTab(e){
      console.log(e)
      const {index}=e.currentTarget.dataset
      const {tabs}=this.data
      tabs.forEach((v,i)=>i===index?v.isActive=true:v.isActive=false)
      this.setData(tabs)
      console.log(this.data.tabs)
    }
  }
})
//点击并不能切换tab
```

3）组件传值

(1)父传子

```js
//组件内定义属性
Component({
  /**
   * 组件的属性列表
   */
  properties: {
    aaa:{//定义属性
      type:String,
      value:""
    }
  },
})
```

调用组件时传值

```html
<tabs aaa="zhanghuan"></tabs>
```

组件内调用属性

```html
<view>
{{aaa}}
</view>
```

(2)子传父

子wxml

```html
<button bindtap="tab" type="primary">向父组件传值</button>
```

子js

```js
// components/tabs/tabs.js
Component({
  /**
   * 组件的属性列表
   */
  properties: {
  },

  /**
   * 组件的初始数据
   */
  data: {
  },
  /**
   * 1 页面.js 事件的回调函数放在data同层级下
   * 2 组件.js 事件的回调函数放在method中
   */

  /**
   * 组件的方法列表
   */
  methods: {
    tab(e){
      this.triggerEvent("ItemChange",1)
    }
  }
})

```

父wxml

```html
<tabs aaa="zhanghuan" bindItemChange="handleItemChange"></tabs>
```

父js

```js
// pages/test3/3.js
Page({
  handleItemChange(e){
    console.log(e.detail)
  }
})
```

4）slot

子组件中定义

```html
<view>
  <slot></slot>
</view>
```

父组件替换

```html
<tabs>
    <block>aaaaaa</block>
</tabs>
```

---

**API**

1）获取手机型号

```js
console.log(wx.getSystemInfoSync().model)
```

2）加载

```js
    wx.showLoading({
      title: '正在加载',
    })
    setTimeout(()=>{
      wx.hideLoading()
    },1500)
```

3）toast

```js
    wx.showToast({
      title: '提交成功',
    })
```

4）跳转

```js
    wx.navigateTo({
      url: '/pages/test2/8',
    })
```

---

**实现案例**

1）滑动横条

```html
<scroll-view  scroll-x>
    <view class="scrollOut">
        <view class="scrollIn"></view>
        <view class="scrollIn"></view>
        <view class="scrollIn"></view>
        <view class="scrollIn"></view>
        <view class="scrollIn"></view>
        <view class="scrollIn"></view>
    </view>
</scroll-view>
```

```css
.scrollOut{
  display: flex;
  flex-wrap: nowrap;
}

.scrollIn{
  width: 100px;
  height: 100px;
  background-color: gold;
  margin-right: 2px;
  flex: 0 0 100px;
}
```

2）提交成功

```html
<view style="padding: 5px;text-align:center;">
    <icon type="success" size="100"></icon>
    <view>提交成功</view>
</view>
```


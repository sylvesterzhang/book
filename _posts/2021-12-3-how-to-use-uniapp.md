---
layout: post
title: uniapp使用
tags: uniapp
categories: front
---

组件、生命周期、tabbar配置、uview、数据缓存



**组件**

1）定义组件

```vue
<template>
	<view>
		card
	</view>
</template>

<script>
	export default {
		name:"card",
		data() {
			return {
				
			};
		}
	}
</script>

<style>

</style>

```

2）导入、注册、使用组件

```vue
<template>
	<view>
		<text>areyou ok</text>
		<card></card>
		111
	</view>
</template>

<script>
	import card from "../../components/card/card.vue"
	export default {
		data() {
			return {
				
			}
		},
		components:{
			card
		},
		methods: {
			
		}
	}
</script>

<style>

</style>

```

---

**生命周期**

1）应用生命周期

```js
	export default {
        //应用初始化完成后，应用只执行一次
		onLaunch: function() {
			console.log('App Launch')
		},
        //应用显示的时候执行，从后台到前台
		onShow: function() {
			console.log('App Show')
		},
        //应用隐藏的时候执行，从前台到后台
		onHide: function() {
			console.log('App Hide')
		}
	}
```

2）页面生命周期

```js
		onload(){
			//加载时执行
		},
		onReady(){
			//初次渲染时触发
		},
		onShow(){
			
		},
		onHide(){
			//页面隐藏时触发
		},
		onUnload(){
			//页面卸载时触发
		},
```

3）组件生命周期

```js
	export default {
		beforeCreate() {
			//创建前
		},
		created() {
			//创建后
		},
		mounted() {
			//挂载后
		},
		destroyed() {
			//销毁后
		}
	}
```

---

**tabbar配置**

配置放到pages.json文件中

```json
	"tabBar": {
	    "color": "#7A7E83",
	    "selectedColor": "#3cc51f",
	    "borderStyle": "black",
	    "backgroundColor": "#ffffff",
	    "list": [{
	        "pagePath": "pages/about/about",
	        "iconPath": "static/images/a2.png",
	        "selectedIconPath": "static/images/a1.png",
	        "text": "关于"
	    }, {
	        "pagePath": "pages/index/index",
	        "iconPath": "static/images/b2.png",
	        "selectedIconPath": "static/images/b1.png",
	        "text": "首页"
	    }]
	}
```

---

**uview**

1）请求封装

(1)在`/config/request.js`中，写入如下内容：

```js
// 此vm参数为页面的实例，可以通过它引用vuex中的变量
module.exports = (vm) => {
    // 初始化请求配置
    uni.$u.http.setConfig((config) => {
        /* config 为默认全局配置*/
        config.baseURL = 'http://localhost:8088'; /* 根域名 */
        return config
    })
	
	// 请求拦截
	uni.$u.http.interceptors.request.use((config) => { // 可使用async await 做异步操作
	    config.header.token = vm.$store.state.token
	    return config 
	}, config => { // 可使用async await 做异步操作
	    return Promise.reject(config)
	})
	
	// 响应拦截
	uni.$u.http.interceptors.response.use((response) => { /* 对响应成功做点什么 可使用async await 做异步操作*/
		var {statusCode,data} = response
		return data === undefined ? {} : data
	}, (response) => { 
		// 对响应错误做点什么 （statusCode !== 200）
		return Promise.reject(response)
	})
}
```

(2)在`main.js`中引用`/config/request.js`

```js
import Vue from 'vue'
import App from './App'

// 假设您项目中已使用VueX
import store from './store'
Vue.prototype.$store = store

Vue.config.productionTip = false
App.mpType = 'app'

// 引入全局uView
import uView from 'uview-ui'
Vue.use(uView)

import mixin from './common/mixin'
Vue.mixin(mixin)

const app = new Vue({
	store,
	...App
})

// 引入请求封装，将app参数传递到配置中
require('/config/request.js')(app)

app.$mount()
```

(3)发送请求

```js
uni.$u.http.get('/list', {params: {userName: 'name', password: '123456'}}).then(res => {
	console.log(res)
}).catch(err => {
	console.log(e)
})
```

```js
uni.$u.http.post('/', {userName: 'name', password: '123456'}).then(res => {
	console.log(res)
}).catch(err => {
	console.log(err)
})
```

2）路由跳转

```js
uni.$u.route('/pages/components/empty/index', {
	name: 'lisa',
	age: 20
});
```

---

**数据缓存**

1）设置值

```js
uni.setStorage({
    key: 'storage_key',
    data: 'hello',
    success: function () {
        console.log('success');
    }
});
```

2）获取值

```js
uni.getStorage({
    key: 'storage_key',
    success: function (res) {
        console.log(res.data);
    }
});
```

3）删除值

```js
uni.removeStorage({
    key: 'storage_key',
    success: function (res) {
        console.log('success');
    }
});
```

4）清除本地缓存

```js
uni.clearStorage();
```

获取微信openid

```js
uni.login({
	provider: 'weixin',
	onlyAuthorize: true,
	success: function(loginRes) {
		console.log(loginRes);

		let appid = 'wx35fd7d175e3c0b22'
		let secret = '71c349ed2c0dcbbd2c16f17c4fb48383'
		let url = 'https://api.weixin.qq.com/sns/jscode2session?appid=' + appid + '&secret=' +
			secret + '&js_code=' +
			loginRes.code + '&grant_type=authorization_code';
		uni.request({
			url: url, // 请求路径
			success: result => {
				console.info(result.data.openid);
			},
		});
	}
})
```


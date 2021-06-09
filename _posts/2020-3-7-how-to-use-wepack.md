---
layout: post
title: webpack打包工具
tags: webpack
categories: frontend
---

概述、快速使用、配置文件、webpack-dev-server、babel使用

**概述**

为优化页面发送多次的二次请求导致加载速度变慢和处理错综复杂的文件依赖关系，则需要将项目中涉及的多个文件进行合并并重新构建。wepack是一个基于node.js项目构建工具，其构建方式是基于项目构建。

---

**安装**

```
npm i webpack -g
```

```
npm i webpack-cli -d
```

```
npm install --save-dev webpack
```

---

**快速使用**

`1`项目中创建main.js

```javascript
import $ from 'jquery'//先安装jQuery，命令npm i jueqry --save
$(function(){
	//代码
})
```

`2`执行命令

低版本

```
webpack ./src/main.js ./dist/bundle.js
```

高版本

```
webpack ./src/main.js -o ./dist/bundle.js
```

`./src/main.js`是要打包的js文件路径，`./dist/bundle.js`是输出路径，webpack能处理js文件相互关系并处理兼容问题

---

**配置文件**

在项目的更目录下创建一个名为webpack.config.js的文件

```javascript
//webpack.config.js
const path=require('path')
module.exports={
	entry:path.join(__dirname,'./src/main.js'),
	output:{
		path:path.join(__dirname,'./dist'),
		filename:'bundle.js'
	}
}
```

直接运行`webpack`便可进行构建

---

**webpack-dev-server**

`1`安装

```
npm i webpack-dev-server -d
```

`2`package.json文件中配置

```
{
  "name": "webpack_file",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev":"webpack-dev-server --open --port 3000 --contentBase src --hot"//新增
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "jquery": "^3.4.1"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11"
  }
}
```
'webpack-dev-server --open --port 3000 --contentBase src --hot'里，open是指运行后打开浏览器，port设置端口号，contentBase设置根目录，hot设置热更新

> 注意，json文件中不可以写注释，这里的注释仅仅是为了方便理解，实际项目中应去除

另一种配置方法则为修改webpack.config.js

```javascript
//webpack.config.js
const path=require('path')
const webpack=require('webpack')
module.exports={
	entry:path.join(__dirname,'./src/main.js'),
	output:{
		path:path.join(__dirname,'./dist'),
		filename:'bundle.js'
	},
    devServer:{
        open:true,
        port:3000,
        contentBase:'src',
        hot:true
    }
    plugins:[
    	new webpack.HotModuleReplacementPlugin()
    ]
}
```

这种配置方法不推荐

`3`运行命令启动服务

```
npm run dev
```

> 注意webpack-dev-server会帮我们生成bundle.js文件，通过http://localhost:3000/bundle.js可访问，此时文件存在于内存中，未放置在物理磁盘里

`4`内存中的bundle.js自动注入html页面中

1）安装

```
npm i html-webpack-plugin -d
```

2）webpack.config.js文件中进行配置

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
    ]
}
```

`5`引入样式文件css、less、sass文件

1）安装

```
npm i style-loader css-loader -d
```

```
npm i less -d
npm i less-loader
```

```
npm i node-scss -d
npm i sass-loader -d
```

2）修改webpack.config.js文件

```javascript
const path=require('path')
const htmlwebpackplugin=require('html-webpack-plugin')
module.exports={
	entry:path.join(__dirname,'./src/main.js'),
	output:{
		path:path.join(__dirname,'./dist'),
		filename:'bundle.js'
    },
    plugins:[
        new htmlwebpackplugin({
            template:path.join(__dirname,'./src/index.html'),
            filename:'index.html'
        })
    ],
    module:{//新增
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']},
            {test:/\.less$/,use：['style-loader','css-loader','less-loader']},
    		{test:/\.scss$/,use：['style-loader','css-loader','sass-loader']}
        ]
    }
}
```

3）main.js中导入样式文件（以css文件为例）

```javascript
import $ from 'jquery'
import './css/index.css'//注意：根目录更改为src，所有这里直接使用地址'./css/index.css'
$(function(){
    alert(123)
})
```

`6`样式文件中引入图片

1）安装

```
npm i url-loader file-loader -d
```

2）修改webpack.config.js文件

```javascript
const path=require('path')
const htmlwebpackplugin=require('html-webpack-plugin')
module.exports={
	entry:path.join(__dirname,'./src/main.js'),
	output:{
		path:path.join(__dirname,'./dist'),
		filename:'bundle.js'
    },
    plugins:[
        new htmlwebpackplugin({
            template:path.join(__dirname,'./src/index.html'),
            filename:'index.html'
        })
    ],
    module:{
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']},
            {test:/\.less$/,use:['style-loader','css-loader','less-loader']},
    		{test:/\.scss$/,use:['style-loader','css-loader','sass-loader']},
            {test:/\.(jpg|png|gif|bmp|jpeg)$/,use:'url-loader'}//新增
			//这里的url-loader可跟参数，如url-loader?limit=7617&name=[hash:8]-[name]-[ext]
			//图片默认会以base64输出，通过limit设置限制，小于这个大小则用默认方式，大于则原样输出
        ]
    }
}
```

3）css文件中引入图片

```css
#main{
    background-image: url(../imgs/123.jpg);
}
```

4）处理字体文件

导入bootstrap会有字体文件需要导入，只需在webpack.config.js文件中module.rules中新增`{test:/\.(ttf|eot|svg|woff|woffz)$/,use:'url-loader'}`

这样配置后我们就可以在main.js中直接导入bootstrap

```javascript
import 'bootstrap/dist/css/bootstrap.css'
```

---

**babel使用**

由于webpack只可处理部分ES6语法，有的语法并不支持如类的静态属性，需要借助外部插件完成对js新语法的支持

`1`安装

```
npm i babel-core babel-loader babel-plugin-transform-runtime -d
```

```
npm i babel-preset-env babel-preset-stage-0 -d
```

`2`修改webpack.config.js文件

```javascript
const path=require('path')
const htmlwebpackplugin=require('html-webpack-plugin')
module.exports={
	entry:path.join(__dirname,'./src/main.js'),
	output:{
		path:path.join(__dirname,'./dist'),
		filename:'bundle.js'
    },
    plugins:[
        new htmlwebpackplugin({
            template:path.join(__dirname,'./src/index.html'),
            filename:'index.html'
        })
    ],
    module:{
        rules:[
            {test:/\.css$/,use:['style-loader','css-loader']},
            {test:/\.less$/,use['style-loader','css-loader','less-loader']},
    		{test:/\.scss$/,use['style-loader','css-loader','sass-loader']},
            {test:/\.(jpg|png|gif|bmp|jpeg)$/,use:'url-loader'},
			{test:/\.js$/,use:'babel-loader',exclude:/node_modules/}//新增
			//这里要排除掉node_modules的js文件，防止运行速度变慢已及项目无法正常运行
        ]
    }
}
```

`3`根目录下新建配置文件.babelrc

```
{
	"presets":["env","stage-0"],
	"plugins":["transform-runtime"]
}
```

> 注意：严格按照json语法，不能写注释，字符串要用双引号

`3`异常处理

运行过程总报错

```
ERROR in ./src/main.js
Module build failed (from ./node_modules/babel-loader/lib/index.js):
Error: Plugin/Preset files are not allowed to export objects, only functions. In C:\Users\admin\Desktop\vue\webpack_vue\node_modules\babel-preset-stage-0\lib\index.js
    at createDescriptor (C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-descriptors.js:178:11)
    at C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-descriptors.js:109:50
    at Array.map (<anonymous>)
    at createDescriptors (C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-descriptors.js:109:29)
    at createPresetDescriptors (C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-descriptors.js:101:10)
    at presets (C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-descriptors.js:47:19)
    at mergeChainOpts (C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-chain.js:320:26)
    at C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-chain.js:283:7
    at Generator.next (<anonymous>)
    at buildRootChain (C:\Users\admin\Desktop\vue\webpack_vue\node_modules\@babel\core\lib\config\config-chain.js:120:29)
```

做如下处理

```
npm uninstall babel-loader
npm install babel-loader@7.1.5
```


`1`安装

```
npm i mavon-editor
```

`2`vue项目内新建文件夹plugin，并创建文件mavoneditor.js

```js
import Vue from 'vue'
import mavonEditor from 'mavon-editor'
import 'mavon-editor/dist/css/index.css'

Vue.use(mavonEditor)

```

`3`在main.js中导入刚创建的mavoneditor.js

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import './plugins/element.js'
import './plugins/mavoneditor.js' //导入

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')

```

`4`模板文件中直接调用

```html
<mavon-editor/>
```

```html
<mavon-editor :value="item.answer" :subfield="false" :defaultOpen="'preview'" :toolbarsFlag="false" :editable="false"
    :scrollStyle="true" :ishljs="true" />
```

`5`上传图片

```html
<mavon-editor ref="md" @imgAdd="$imgAdd" previewBackground="white" :boxShadow="false"/>
```

```js
export default{
    methods:{
    $imgAdd(pos, $file){
            // 第一步.将图片上传到服务器.
            console.log(pos,$file)
           var formdata = new FormData();
           formdata.append('file', $file);
           axios({
               url: this.$store.state.upload_base_url+ '/api/upload',
               method: 'post',
               data: formdata,
               headers: { 'Content-Type': 'multipart/form-data' },
           }).then((data) => {
               // 第二步.将返回的url替换到文本原位置![...](0) -> ![...](url)
               // $vm.$img2Url 详情见本页末尾
               //$vm.$img2Url(pos, url);
               let url=this.$store.state.upload_base_url.replace('index.php','')+data.data.data
               console.log(url)
               this.$refs.md.$img2Url(pos, url);
           })
        }
  }
}
```

`6`去除背景色

在vue的updated方法中通过jquery设置属性

```js
updated() {
    $(".v-note-wrapper").css("box-shadow", "").css("min-height", "4rem")//设置最低高度
    $(".v-show-content").css("background-color", "")//去除背景色
}
```


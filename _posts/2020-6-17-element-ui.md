---
layout: post
title: elementui
tags: frontend
categories: frontend
---

安裝、Button组件、文字链接组件、Layout栅格布局、Container容器、Radio组件、CheckBox组件、Input组件、Select选择器、Switch组件、时间日期组件、Upload组件、Form表单、消息提示、表格

**安裝**

`1`安装到项目中

在初始化好Vue项目后执行以下命令

```
npm i element-ui -s
```

也可以通过图形界面进行安装

`2`导入依赖并注入到Vue中

打开main.js文件，写入以下

```javascript
import ElementUi from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(elementui)
```

---

**Button组件**

```html
<el-button type="primary">按钮</el-button>
```

type属性表示按钮的颜色类型

`1`其他属性

| 属性键 | 值类型  | 含义         |
| ------ | ------- | ------------ |
| size   | string  | 大小         |
| plain  | boolean | 是否朴素按钮 |
| round  | boolean | 是否圆角     |
| circle | boolean | 是否时圆     |
| icon   | string  | 图标         |

`2`按钮组

```html
    <el-button-group>
      <el-button type="primary">are you ok</el-button>
      <el-button type="primary">are you ok</el-button>
    </el-button-group>
```

---

**文字链接组件**

```html
<el-link :underline="false">文字链接</el-link>
```

underline属性设置是否去掉下划线，此外还要disabled，icon属性

---

**Layout栅格布局**

```html
    <el-row>
      <el-col :span="6">a</el-col>
      <el-col :span="6">b</el-col>
      <el-col :span="6">c</el-col>
      <el-col :span="6">d</el-col>
    </el-row>
```

每行分24栏，span属性表示这个格子占用多少栏

`1`el-row属性

| 属性键 | 值类型 | 含义                                  |
| ------ | ------ | ------------------------------------- |
| gutter | number | 栅格间隔，单位为px                    |
| type   | string | 布局模式，可选 flex，现代浏览器下有效 |
| tag    | string | 自定义元素标签                        |

`2`el-col属性

| 属性键 | 值类型 | 含义         |
| ------ | ------ | ------------ |
| offset | number | 偏移格数     |
| push   | number | 向右移动格数 |
| pull   | number | 向左移动格数 |

---

**Container容器**

```html
    <el-container>
      <el-header></el-header>
      <el-container>
        <el-aside></el-aside>
        <el-main></el-main>
      </el-container>
      <el-footer></el-footer>
    </el-container>
  </div>
```

el-container的子元素没有el-header或el-footer时，容器为水平

---

**Radio组件**

```html
<el-radio v-model="radio" label="1">备选项</el-radio>
```

label表示该标签的属性值，需配合v-model使用

`1`按钮组

```html
    <el-radio-group v-model="radio">
      <el-radio :label="3" border>备选项</el-radio>
      <el-radio :label="6" border>备选项</el-radio>
      <el-radio :label="9" border>备选项</el-radio>
    </el-radio-group>
```

要实现默认选中效果，外部需套上el-radio-group标签，并将v-model设置在该标签上

`2`事件

```html
    <el-radio-group v-model="radio" @change="dochange">
      <el-radio :label="3" border>备选项</el-radio>
      <el-radio :label="6" border>备选项</el-radio>
      <el-radio :label="9" border>备选项</el-radio>
    </el-radio-group>
```

在属性组上添加change事件，一旦内容发生变化会触发该事件绑定的方法

---

**CheckBox组件**

```html
<el-checkbox v-model="checked">备选项</el-checkbox>
```

对于checkbox，tue-lable属性表示选中后提交的值，而lable属性仅在该标签在复选框组时有效

单个checkbox只能选则一个，要完成多选，需使用复选框组

```html
    <el-checkbox-group v-model="checkvalues">
      <el-checkbox :label="1">备选项1</el-checkbox>
      <el-checkbox :label="2">备选项1</el-checkbox>
      <el-checkbox :label="3">备选项1</el-checkbox>
    </el-checkbox-group>
```

min属性控制最少可选择的数目，max属性控制最多可选择的数目

---

**Input组件**

```html
<el-input v-model="input" placeholder="请输入内容"></el-input>
```

`1`常用属性

| 属性键      | 值类型  | 含义             |
| ----------- | ------- | ---------------- |
| type        | string  | 类型，如textarea |
| maxlength   | number  | 输入内容最大长度 |
| minlength   | number  | 输入内容最小长度 |
| placeholder | string  | 占位文字         |
| clearable   | boolean | 是否可清空       |
| prefix-icon | string  | 前缀图标         |
| suffix-ico  | string  | 后缀图标         |

`2`事件

| 事件   | 触发条件                 |
| ------ | ------------------------ |
| blur   | 失去焦点                 |
| focus  | 获取焦点                 |
| change | 输入框失去焦点或点击回车 |
| input  | input值改变时            |
| clears | 清除时                   |

`3`调用input的focus方法

```html
<template>
	<el-input
      v-model="state"
      placeholder="请输入内容"
      :maxlength="5"
      show-word-limit
      ref="inputs"
    ></el-input>
    <el-button @click="getfocus">点击获取焦点</el-button>
</template>

<script>
  export default {
    name: 'HelloWorld',
    methods: {
      getfocus(){
        this.$refs.inputs.focus()
        //绑定ref=inputs的对象调用focus方法
      }
    },

  }
</script>
```

---

**Select选择器**

```html
<el-select v-model="city">
  <el-option value="上海" label="上海"></el-option>
  <el-option value="北京" label="北京"></el-option>
</el-select>
```

multiple属性表示是否能多选

---

**Switch组件**

```html
<el-switch
  v-model="onoff"
  active-text="按月付费"
  inactive-text="按年付费">
</el-switch>
```

active-text、inactive-text、active-value、inactive-value

---

**时间日期组件**

`1`timepicker下拉选择

```html
<el-time-select
  v-model="value"
  :picker-options="{
    start: '08:30',
    step: '00:15',
    end: '18:30'
  }"
  placeholder="选择时间">
</el-time-select>
```

`2`datepicker

```html
    <el-date-picker
      v-model="value1"
      type="week"
      format="yyyy 第 WW 周"
      placeholder="选择周">
    </el-date-picker>
```

type可以是week,date,datetime

小于当前日期的不可选案例

```html
<template>
    <el-date-picker
      v-model="value1"
      type="date"
      :picker-options="option"
      placeholder="选择日期">
    </el-date-picker>
  </div>
</template>
<script>
  export default {
      data:()=>{
          return {
              option:{
                  disabledDate(time){
                    return time.getTime()<Date.now()
              }
        }
          }
      }
  }
<script>
```



---

**Upload组件**

```html
  <div>
    <el-upload action="https://jsonplaceholder.typicode.com/posts/">
      <el-button>点击上传</el-button>
    </el-upload>
  </div>
```

注意：没有action会报错

| 属性键         | 值类型  | 含义                         |
| -------------- | ------- | ---------------------------- |
| file-list      | string  | 展示上传的文件列表           |
| show-file-list | boolean | 是否展示                     |
| accept         | number  | 限制文件类型                 |
| limit          | number  | 限定上传个数                 |
| acced          | string  | 文件个数超过限定个数执行方法 |

---

**Form表单**

`1`表单在鼠标移开时验证

```html
<template>
  <div>
   <el-form :model="ruleForm" :rules="rules" label-width="100px">
       <!--
		1.表单必须配置model属性，formitem的子标签必须有v-model
		2.rule属性指向data里的rules，在里面定义验证规则
		-->
     <el-form-item label="活动名称" prop="name">
      <el-input v-model="ruleForm.name"></el-input>
    </el-form-item>
   </el-form>
  </div>
</template>
<script>
  export default {
    name: 'HelloWorld',
    data:()=>{
      return {
        ruleForm: {
          name: '',
        },
        rules:{
          name: [//name指向formitem的prop属性
            { required: true, message: '请输入活动名称', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
        }
      }
    },

  }
</script>
```

`2`提交时验证

点击提交按钮时，去调用表单自身的validate方法，并传入对不同结果的后序处理函数

```html
<template>
  <div>
   <el-form :model="ruleForm" :rules="rules" ref="dovalid" label-width="100px">
     <el-form-item label="活动名称" prop="name">
      <el-input v-model="ruleForm.name"></el-input>
    </el-form-item>
     <el-form-item >
       <el-button @click="sub">提交</el-button>
     </el-form-item>
   </el-form>
  </div>

</template>

<script>
  export default {
    name: 'HelloWorld',
    data:()=>{
      return {
        ruleForm: {
          name: '',
          region: '',
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        },
        rules:{
          name: [
            { required: true, message: '请输入活动名称', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
        }
      }
    },
    methods: {
      sub(){
         this.$refs.dovalid.validate((valid) =>{
           if(valid){
             console.log("ok")
           }else{
             console.log("not ok")
             return false
           }
         })
      }
    },

  }
</script>
```

`3`自定义验证

```html
<template>
  <div>
   <el-form :model="ruleForm" :rules="rules" ref="dovalid" label-width="100px">
     <el-form-item label="活动名称" prop="name">
      <el-input v-model="ruleForm.name"></el-input>
    </el-form-item>
     <el-form-item >
       <el-button @click="sub">提交</el-button>
     </el-form-item>
   </el-form>
  </div>

</template>

<script>
  export default {
    name: 'HelloWorld',
    data:()=>{
      var validatePass = (rule, value, callback) => {//定义好验证规则
        if (value === '') {
          callback(new Error('不可以为空'));
        } 
        callback
      };
      return {
        ruleForm: {
          name: '',
          region: '',
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        },
        rules:{
          name: [
            { validator:validatePass, trigger: 'blur' },//将验证规则注册进去
          ],
        }
      }
    },
    methods: {
      sub(){
         this.$refs.dovalid.validate((valid) =>{
           if(valid){
             console.log("ok")
           }else{
             console.log("not ok")
             return false
           }
         })
      }
    },

  }
</script>
```

---

**消息提示**

`1`alert消息

```html
   <el-alert title="主信息" center type="success">
     <div slto>辅助信息</div>
   </el-alert>
```

`2`message消息

```html
<template>
	<el-button @click="msg">点击提示</el-button>
</template>
<script>
export default {
    name: 'HelloWorld',
  methods:{
    msg(){
        const h=this.$createElement
        this.$message({
            message:h('p',null,"提示1"),
            showClose:true
        })
    }
  }
}
</script>
```

关闭消息，调用this.@message.closeAll()

---

**表格**

`1`基本使用

```html
<template>
  <div>
    <el-table :data="options">
       <el-table-column
      type="selection"
      width="55">
    </el-table-column>
      <el-table-column prop="name" label="姓名"></el-table-column>
      <el-table-column prop="age" label="年龄"></el-table-column>
      <el-table-column prop="job" label="工作"></el-table-column>
    </el-table>
  </div>

</template>

<script>

export default {
  name: 'HelloWorld',
  data:()=>{
    return {
      options:[
        {name:"zhang",age:22,job:"coder"},
        {name:"zhang1",age:23,job:"coder"},
        {name:"zhang2",age:24,job:"coder"}
      ]
    }
  },
  methods:{

  }
}
</script>
```

`2`排序

将要排序的列el-table-column标签上加上sortable属性

`3`格式化

```html
<template>
  <div>
    <el-table :data="options">
       <el-table-column
      type="selection"
      width="55">
    </el-table-column>
      <el-table-column prop="name" label="姓名"></el-table-column>
      <el-table-column prop="age" sortable  label="年龄"></el-table-column>
      <el-table-column prop="job" label="工作"></el-table-column>
      <el-table-column prop="sex" label="性别" :formatter="formatsex"></el-table-column><!--指向方法-->
    </el-table>
  </div>

</template>

<script>

export default {
  name: 'HelloWorld',
  data:()=>{
    return {
      options:[
        {name:"zhang",age:22,job:"coder",sex:1},
        {name:"zhang1",age:23,job:"coder",sex:0},
        {name:"zhang2",age:24,job:"coder",sex:0}
      ]
    }
  },
  methods:{
    formatsex(row,cpl,val){//定义格式化规则
      if(val==0){
        return '女'
      }else{
        return '男'
      }
    }
  }
}
</script>
```

`3`获取select选中的数据

在el-table标签上定义select事件，指向一个方法，此方法有两个参数，分别是selection,row

`4`设置默认全选中

```js
this.$refs.multipleTable.toggleAllSelection();
```

数据调用成功后，在js中执行此方法

`5`表格数据更新后不重新渲染问题

将更新数据成功发到后端后，需要将表格中显示的数据更新。当重新获取数据时，发现表格并不一定会渲染新数据。所以我们不必重新获取数据，直接将原来的数据进行修改即可

```js
this.$set(this.addresses,this.address.$index,this.address)
//第一个参数是原来的数组数据，第二个参数是下标，第三个参数是修改后的数据
```

`6`表格合并

1）数据新增标识符

```js
//省略部分代码
getorder() {
    let token = localStorage.getItem('token')
    if (token != null) {
        this.$axios.get(this.$store.state.base_url + "/order/?token=" + token + "&page=" + this.order_page +
            "&limit=" + this.order_limit).then(data => {
            //console.log(data)
            let origin_orders = data.data.data.data
            let new_orders = []
            //数据进行处理
            for (const iterator of origin_orders) {
                let identification = true //用于表格合并，标记订单下的第一条数据
                let orderLength = iterator.orderToCommodities.length //标记订单长度
                for (const item of iterator.orderToCommodities) {
                    let obj = {}
                    obj.identification = identification
                    obj.orderLength = orderLength
                    obj.addressId = iterator.addressId
                    obj.id = iterator.id
                    obj.total = iterator.total
                    obj.status = iterator.status
                    obj.payment = iterator.payment
                    obj.createTime = iterator.createTime
                    obj.commodityNum = item.commodityNum
                    obj.commodityName = item.commodity.commodityName
                    obj.commodityId = item.commodity.commodityId
                    obj.pic = item.commodity.listPic
                    obj.price = item.commodity.price
                    new_orders.push(obj)
                    identification = false
                }
            }
            this.orders = new_orders
            this.order_total = data.data.data.page.total
            console.log(new_orders)

        })
    } else {
        this.$message.error("请先进行登陆");
        this.$router.push("login")
    }
},
```

2）表格上新增span-method属性指向一个渲染方法

```html
<el-table :data="orders" :span-method="objectSpanMethod" style="width: 100%; margin-top: 20px">
    <el-table-column prop="id" label="订单号" width="180">
    </el-table-column>
    <el-table-column prop="commodityName" label="商品" width="400">
    </el-table-column>
    <el-table-column prop="price" label="单价" width="100">
    </el-table-column>
    <el-table-column prop="commodityNum" label="数量" width="50">
    </el-table-column>
    <el-table-column prop="total" label="合计" width="100">
    </el-table-column>
    <el-table-column prop="status" label="状态" width="50">
    </el-table-column>
    <el-table-column prop="createTime" label="下单时间">
    </el-table-column>
    <el-table-column label="操作">
        <template slot-scope="scope" width="150">
            <el-link type="primary" @click="order_pay(scope.$index,scope.row)" style="margin-left:1rem">付款</el-link>
            <el-link type="primary" style="margin-left:1rem">删除</el-link>
        </template>
    </el-table-column>
</el-table>
```

3）定义渲染方法

```js
objectSpanMethod({
    row,//行对象 
    column,//当前格对象
    rowIndex,//行下标 int
    columnIndex//列下标 int
}) {
    console.log(row, column, rowIndex, columnIndex)
    let identification = row.identification
    let orderLength = row.orderLength
    if (columnIndex === 0 | columnIndex === 4 | columnIndex === 5 | columnIndex === 6 | columnIndex === 7) {
        if (identification == true) {
            return {
                rowspan: orderLength,
                colspan: 1
            };
        } else {
            return {
                rowspan: 0,
                colspan: 0
            };
        }
    }
},
```



---

**图片上传**

```html
<el-upload :multiple="false" :action="this.$store.state.upload_base_url+'/api/upload'"
    :on-success="handlePreview_detail">
    <el-button size="small" type="primary">点击上传</el-button>
    <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
</el-upload>
```

action属性指向文件上传的目标地址，on-success指向文件上传成功后指向的方法，该方法以一个参数来接收上传文件后目标地址的返回值

```js
//方法
handlePreview_detail(response) {
    console.log(response)
    let url = this.$store.state.upload_base_url.replace('index.php', '') + response.data//根据返回值拼出图片地址
    this.detailPic = url//将图片渲染出来
},
```

---

**树形控件**

```html
<el-tree :data="classification_data" :props="defaultProps" node-key="id" default-expand-all
    :expand-on-click-node="false">
    <span class="custom-tree-node" slot-scope="{ node, data }"><!--渲染主体，node,data是主体内的全局变量-->
        <span>{{ node.label }}</span>
        <span style="margin-left:2rem;">
            <el-button type="text" size="mini" @click="() => append(data)">
                新增
            </el-button>
            <el-button type="text" size="mini" @click="() => edit(data)">
                编辑
            </el-button>
            <el-popconfirm confirmButtonText='确定删除' cancelButtonText='不用了' @onConfirm="remove(node, data)"
                icon="el-icon-info" iconColor="red" title="确定要删除该节点以及其所有子节点吗？">
                <el-button slot="reference" type="text" size="mini">删除</el-button>
            </el-popconfirm>
        </span>
    </span>
</el-tree>
```

| 控件属性             | 解释                                                 |
| -------------------- | ---------------------------------------------------- |
| data                 | 需要渲染的数据                                       |
| props                | label、children等默认属性，需将label与源数据进行对应 |
| node-key             | 节点的唯一标识                                       |
| default-expand-all   | 默认展开树                                           |
| expand-on-click-node | 是否是点击后才显示子节点                             |

---

**分页插件**

```html
<el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="current_page"
    :page-sizes="[10, 20, 30, 40]" :page-size="current_limit" layout="total, sizes, prev, pager, next, jumper"
    :total="total">
</el-pagination>
```

| 属性名(事件)    | 解释                                                         |
| --------------- | ------------------------------------------------------------ |
| @size-change    | 每一页显示数量改变后触发方法，对应方法可接受一个参数，改变后的新值 |
| @current-change | 当前所在页改变后触发方法，对应方法可接受一个参数，改变后的新值 |
| :current-page   | 当前在的页码位置                                             |
| :page-sizes     | 可选的每页显示数量                                           |
| :page-size      | 当前每页显示的数量                                           |
| layout          | 控件用到的功能                                               |
| :total          | 数据总数                                                     |

---

**级联选择器**

```html
<el-cascader :options="classifications" v-model="classificationId"
    :props="{ checkStrictly: true,label:'classification',value:'id',emitPath:false }" clearable>
</el-cascader>
```

optins指向被渲染的数据，v-model指向选择后的值

props属性

| props属性     | 解释                                                         |
| ------------- | ------------------------------------------------------------ |
| checkStrictly | 是否严格的遵守父子节点不互相关联，默认false                  |
| label         | 指向被渲染的每一条数据的属性，对应需显示的内容               |
| value         | 指向被渲染的每一条数据的属性，对应该条数据的值               |
| emitPath      | 在选中节点改变时，是否返回由该节点所在的各级菜单的值所组成的数组，若设置 false，则只返回该节点的值 |

---

**图片组件**

当图片组件的src设置为一个动态变量，并且这个动态变量是需要等待数据请求成功后才会赋值。由于数据请求的事件可能会长，而vue一级完成渲染，src所绑定的变量并未赋值，此时浏览器发出请求后必然会失败，vue便认为图片请求失败，从而会导致页面渲染后出现*图片加载失败*的现象。解决这个问题，需要在el-image标签上加v-if属性，即如果数据请求成功后再进行渲染图片，防止渲染时机不对而导致失败，如下

```html
<template>
	<el-image fit="fill" style="width:400px;height:400px;" v-if="commodity.detailPic" :lazy="true" :src="img_url"></el-image>
</template>
<script>
    export default {
        data: () => {
            return {
                commodity:{}
            }
        },
        updated(){
            let id=this.$route.query.id
            this.$axios.get(this.$store.state.base_url+'/commodity/'+id).then(data=>{
                this.commodity=data.data
            })
        }
    }
</script>
```


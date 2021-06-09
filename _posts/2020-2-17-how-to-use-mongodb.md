---
layout: post
title: mongodb的使用
tags: mongodb
categories: database
---

下载地址、安装、启动服务、查看、创建数据库及表、新增数据、删除数据、更新数据、查询数据、索引

**下载地址**

http://dl.mongodb.org/dl/win32/x86_64

下载名为mongodb-win32-x86_64-2012plus-v4.2-latest-signed.msi的文件

---

**安装**

点击下一步，跳过安装mangodb_compass

---

**启动服务**

```
mongod --dbpath pathdir
```

pathdir就是数据库文件创建的文件夹位置，执行命令后将会在文件夹里生成数据库文件

---

**查看**

```
//查看所有数据库
show dbs

//查看所有表
use db01
show collections
```

---

**创建数据库及表**

```
use db01
db.user.insert({'name':'张欢','age':21,'sex':'male','job':'coder'})
```

执行该代码后会新增数据库db01，并且会在里面新增一张表(集合)user，同时里面会有一条数据，如果只想创建表执行`db01.createCollection('user')`

---

**新增数据**

```
db.user.insert({'name':'张欢','age':21,'sex':'male','job':'coder'})
```

表里插入一条数据，注意在shell里只允许单条插入

---

**删除数据**

```
//数据库删除
use shop
db.dropDatabase()

//表删除
db.user.drop()

//数据删除
db.user.remove({'age':60},{justOne:true})//删除一条满足该条件的数据
db.user.remove({'age':60})//删除满足该条件的所有数据
db.user.remove({})//删除表里所有数据
```

---

**更新数据**

```
db.user.update({'name':'欢'},{$set:{'name':'坤坤'}})
```

注意：仅可对一条数据进行修改

---

**表查询**

```
//查看表中所有数据
db.user.find()

//条件查询
//1）不等式
db.user.find({'age':20})//年龄等于20
db.user.find({'age':{$gt:20}})//年龄大于20
db.user.find({'age':{$lt:30}})//年龄小于30
db.user.find({'age':{$gte:20}})//年龄大于等于20
db.user.find({'age':{$lte:20}})//年龄小于等于30
db.user.find({'age':{$gt:20,$lt:30}})//年龄大于20小于30
//2）包含
db.article.find({'title':/病毒/})
//3）以……开头
db.article.find({'title':/^火神山/})

//查询数据并显示指定字段数据(select age,name,sex from user)
db.user.find({},{'age':1,'name':1,'sex':1})//前一个对象是条件，后一个是要展示的字段

//对查询结果进行排序
//1)升序
db.user.find().sort({'age':1})
//2）降序
db.user.find().sort({'age':-1})

//当前条件下取前5条数据
db.user.find().limit(5)

//取跳过10条数据向后取10条数据
db.user.find().skip(10)

//条件or查询
db.user.find({$or:[{'name':'欢'},{'age':21}]})

//条件and查询
db.user.find({'name':'小毛','age':20})

//结果数量统计
db.user.find().count()
```

---

**索引**

`1`查询哪些字段设置了索引

```
db.user.getIndexes()
```

`2`设置索引

```
db.user.ensureIndex({'username':1})
```

`3`删除索引

```
db.user.dropIndex({'username':1})
```

`4`复合索引

```
db.user.ensureIndex({'username':1,'age':-1})
```

1表示username键按升序存储，-1表示age键按降序存储

`5`删除复合索引

```
db.user.dropIndex({'username':1,'age':-1})
```

`6`设置唯一索引

```
db.user.ensureIndex('user_id':1,'unique':true)
```

`7`查询语句执行时间

```
db.user.find({'name':'zhang'}).explain('executionStats')
```


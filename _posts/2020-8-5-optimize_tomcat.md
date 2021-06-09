---
layout: post
title: tomcat优化
tags: tomcat优化
categories: backends
---

tomcat安装与配置、优化内容、JMeter、JVM字节码

**tomcat安装与配置**

官网下载后上传到服务器

```shell
$ tar -xvf apache-tomcat-8.5.57.tar.gz
```

1）修改用户

```shell
$ vi conf/tomcat-users.xml
```

```xml
  <role rolename="manager"/>
  <role rolename="manager-gui"/>
  <role rolename="admin"/>
  <role rolename="admin-gui"/>
  <user username="tomcat" password="tomcat" roles="manager,manager-gui,admin,admin-gui"/>

```

2）修改配置文件

```shell
$ viMETA-INF/context.xml
```

```xml
[root@localhost META-INF]# cat context.xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<Context antiResourceLocking="false" privileged="true" >
  <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />--><!--注释掉-->
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>

```

3）启动

回到包内的bin目录下

```shell
$ ./startup.sh
```

---

**优化内容**

`1`AJP（Appache JServer Protocol）

WEB服务器和Servlet容器通过TCP连接来交互；为了节省SOCKET创建的昂贵代价，WEB服务器会尝试维护一个永久TCP连接到servlet容器，并且在多个请求和响应周期会重用连接。但多数情况下我们使用的是Ngingx+tomcat架构，所以用不着AJP协议，需要禁用。在tomcat配置文件xml找到8009端口的配置，注释掉即可。优化后吞吐量得到提升。

```xml
<!--<Connector protocol="AJP/1.3"
               address="::1"
               port="8009"
               redirectPort="8443" />--><!--进行注释-->
```

`2`执行器（线程池）

使用执行器提高性能，修改配置文件conf/server.xml，打开注释

```xml
<Executor name="tomcatThreadPool" namePrefix="catalina-exec=" maxThreads="500" minSpareThreads="50" prestartminSpareThreads="true" maxQueueSize="100"/>
<!--配置生效，但页面上会显示最大连接数为-1-->

<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443"/>
<!--连接器指向执行器-->
```

优化后吞吐量提升，平均响应时间减少

1）增大线程池的线程数

并不是线程数越大性能越高

2）增大最大等待队列数

吞吐率提高，响应时间减少，但错误率提高了（12306的处理方式）

`3`3中运行模式

1）bio

默认的io方式，性能低

2）nio

非阻塞的io方式，性能较高

3）apr

从操作系统级别来解决异步io的问题，大幅提示性能

设置运行模式为nio，性能得到提升

```xml
        <Connector port="8080" protocol="org.appache.coyote.http11.http11Nio2Protocol"
                   connectionTimeout="20000"
                   redirectPort="8443" />
```

`4`优化JAVA虚拟机

1）增大堆内存

2）使用G1垃圾回收器

---

**JMeter**

`1`一般步骤

创建测试计划

添加线程组

添加HTTP请求

添加查看结果数

添加聚合报告

添加用表格查看结果

`2`其他说明

每次启动前都要清空上一次的结果

---

**JVM字节码**

1）将字节码文件内容输出到文本文件中

```shell
$ javap -v test1.class >test1.txt
```

2）查看字节码内容

第一部分：生成这个class的java源文件、版本信息、生成时间

第二部分：该类中锁涉及到的常量池

第三部分：显示该类构造器，源码未定义构造器，编译器会自动生成

第四部分：main方法的信息

3）i++与++i的区别

i++表示先返回再+1，++i表示先+1再返回

4）字符串拼接效率

```java
    public void m1(){
        String str="";
        for (int i = 0; i <5 ; i++) {
            str=str+i;
        }
        System.out.println (str);
    }
    public void m2(){//效率高
        StringBuilder str=new StringBuilder ();
        for (int i = 0; i <5 ; i++) {
            str.append (i);
        }
        System.out.println (str);
    }
```

5）代码优化

(1)尽可能使用局部变量

临时变量保持在栈中速度快，随方法运行结束而销毁，不需要额外垃圾回收

(2)尽量减少变量的重复运算

```java
for(int i=o;i<list.size();i++)
{}
```

替换成

```java
int size=list.size();
for(int i=o;i<size;i++)
{}
```

(3)尽量采用懒加载策略

```java
String str="aaa";
if(i==1){
    list.add(str);
}
```

替换成

```java
if(i==1){
    String str="aaa";
    list.add(str);
}
```

(5)不要将数组声明未public static final

(6)不要创建一些不使用的对象，不要导入一些不使用的类

(7)程序运行过程中避免使用反射

(8)使用数据库连接池和线程池

(9)容器初始化时尽可能指定长度

(10)ArrayList遍历快，LinkList添加删除快

(11)使用Entry遍历Map

(12)不要受到调用System.gc()

(13)String尽量少用正则表达式

(14)对资源的close()分开操作
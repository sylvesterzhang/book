---
layout: post
title: SpringBoot
tags: java
categories: backends
---

Spring存在的优缺点、Springboot简介、快速开始、热部署、集成mybatis、mapper-spring-boot-starter、yml配置文件、集成测试、整合redis、idea快速创建Springboot项目、登陆注册案例

**Spring存在的优缺点**

`1`优点

通过依赖注入和面向切面编程，用简单的java对象实现Ejb功能

`2`缺点

组件虽然是轻量级的，配置是重量级的，项目依赖管理耗时，选错版本则会带来一些列兼容问题

---

**Springboot简介**

基于约定优于配置，让开发仍有不必在配置于业务逻辑间进行思维切换，从而提高效率

特点：

1）基于Spring的开发提供更快速入门体验，开箱即用，无代码生成，无需xml配置，可修改默认配置来满足特定需求

2）提供大型项目中常见的非功能特性，如嵌入式服务器，安全、指标、健康检测、外部配置等

3）Springboot不是对Spring功能的增强，而是提供更快速使用Spring的方式

核心功能：

1）起步依赖

本质是一个maven项目对象模型，定义了对其他库的传递依赖，这些东西加载一起即支持某项功能

2）自动配置

运行时的过程，考虑众多因素后决定Spring配置应该选用那个，不用那个，由Spring自动完成

依赖和启动器

1）依赖

核心依赖在父工程中，引入springboot依赖时，无需指定版本

2）启动器

springboot定义的某种功能场景的依赖集合，如mapper-spring-boot-starter，是对ibatis的依赖的高级封装

---

**快速开始**

`1`pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <groupId>org.zhanghuan</groupId>
    <artifactId>springboot_project01</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>


</project>
```

`2`application.java

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;

//@SpringBootApplication;//第一种方式：定义springboot引导类
@EnableAutoConfiguration//第二种方式：定义springboot引导类
@ComponentScan("controller")//第二种方式需要定义扫描范围
public class application {
    public static void main(String[] args) {
        SpringApplication.run (application.class);//运行引导类以创建应用
    }
}
```

`3`quickController.java

```java
package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller//表示该类是一个控制器
public class quickController {

    @RequestMapping("/quick")//定义请求路径
    @ResponseBody//定义返回值作为请求的response
    public String quick(){
        return "hello";
    }
}

//@RestController
//这个注解表示返回json数据

//@GetMapping("/quick")
//get方式发出的请求进行url匹配

//@PostMapping("/quick")
//post方式发出的请求进行url匹配
```

---

**热部署**

每次修改代码，默认情况下都需要重新启动项目。为达到修改后立即生效、无需重启的目的，需要将idea开启热部署

`1`导入依赖

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>
```

`2`settins->Compiler->勾选Build project automatically

`3`同时按下Shift+Ctrl+Alt+/，并选中compiler.automake.allow.when.app.running

---

**集成mybatis**

`1`依赖

```xml
		<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
```

`2`application.properties配置mysql

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test1?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=
```

> 注意
>
> 引入mybatis后必须配置mysql，否则项目无法运行

`3`application.properties配置mybatis

```
mybatis.type-aliases-package=domain
mybatis.mapper-locations=classpath:mappers/*Mapper.xml
```

这是xml的mapper配置，如果sql语句是直接注解在接口的方法上，可用@MapperScan("mapper接口的路径")在入口类上注释即可

`4`application.properties配置端口、虚拟路径

```
server.port=8080
server.servlet.contex-path=/demo
```

`5`案例

1）实体类

```java
package domain;

public class Account {
    private int id;
    private String name;
    private int money;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getMoney() {
        return money;
    }

    public void setMoney(int money) {
        this.money = money;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", money=" + money +
                '}';
    }
}

```

2）Accountmapper.java

```java
package mappers;

import domain.Account;

import java.util.List;

public interface AccountMapper {
    List<Account> findAll();
}

```

3）Accountmapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="mappers.AccountMapper">
    <select id="findAll" resultType="Account">
        select * from account
    </select>
</mapper>
```

4）控制器

```java
package controller;

import domain.Account;
import mappers.AccountMapper;
import org.apache.ibatis.session.SqlSessionFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.List;

@Controller
public class quickController {

    @Autowired
    private SqlSessionFactory factory;
    
    @RequestMapping("/quick")
    @ResponseBody
    public List<Account> quick(){
        AccountMapper accountMapper=factory.openSession ().getMapper (AccountMapper.class);
        List<Account> list=accountMapper.findAll ();
        return list;
    }
}

```

---

**mapper-spring-boot-starter**

这个启动器是对mybatis的高级封装，包含mybatis，数据库连接池hicari，persistence，mapper接口自带一些简单的增删改查方法，无需去进行配置，实现高效开发

`1`依赖

```xml
		<dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency>
```

`2`启动类上标识mapper扫描

```java
package com.zhanghuan.springboot610;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

@SpringBootApplication
@MapperScan("com.zhanghuan.springboot610.mapper")
public class Springboot610Application {

    public static void main(String[] args) {
        SpringApplication.run (Springboot610Application.class, args);
    }

}

```

`3`mapper接口

```java
package com.zhanghuan.springboot610.mapper;

import com.zhanghuan.springboot610.entity.Book;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;
import tk.mybatis.mapper.common.Mapper;

import java.util.List;

@Repository
public interface BookDao extends Mapper<Book> {
}

```

`4`实体类

```java
package com.zhanghuan.springboot610.entity;

import lombok.Data;
import tk.mybatis.mapper.annotation.KeySql;

import javax.persistence.Column;
import javax.persistence.Id;
import javax.persistence.Table;
import javax.persistence.Transient;

import java.util.Date;

@Data
@Table(name="book")//指定表
public class Book {
    @Id
    @KeySql(useGeneratedKeys = true)
    @Column(name="`id`")
    private long id;

    @Column(name="`name`")//指定列，注意需要``包裹列名，否则报错
    private String name;

    @Column(name="`describe`")
    private String describe;

    @Column(name="`price`")
    private double price;

    @Column(name="`status`")
    private int status;

    @Transient//进行查询时，将忽略该列
    @Column(name="`createAt`")
    private Date createAt;
}

```

`5`控制器测试

```java
package com.zhanghuan.springboot610.controller;

import com.zhanghuan.springboot610.entity.Book;
import com.zhanghuan.springboot610.mapper.BookDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
@CrossOrigin//解决跨域问题，注意只可放在控制器上，且springboot版本要求4.2以上
public class bookController {

    @Autowired
    BookDao bookDao;

    @GetMapping("/api/book")
    public List<Book> findall(){
        return bookDao.selectAll ();//使用mapper自带方法
    }
}

```



---

**yml配置文件**

yml和yaml结尾的文件

`1`各类型数据存储

1）键值对

```
name: zhanghuan
```

> 注意
>
> 冒号后面必须有一个空格

2）对象

```
person:
	name: zhanghuan
	age： 21
```

行内写法

```
person: {name: zhanghuan,age: 21}
```

3）集合<普通字符串>

```
city:
	-beijing
	-shanghai
```

4）集合<对象>

```
Student:
	-name: zhanghuan
	 age: 21
	-name: zhanghuan1
	 age: 22
```

行内写法

```
Student: [{name: zhanghuan,age: 21},{name: zhanghuan,age: 21}]
```

5）MAP

```
hashmap:
	key1: value1
	key2: value2
```

`2`从配置文件中取出数据

通过value注解获取

```java
@Controller
public class quickController {
    @Value ("${spring.datasource.url}")//spring的El表达式
    private String url;

    @RequestMapping("/quick")
    @ResponseBody
    public String quick(){
        System.out.println (url);
        return url;
    }
}
```

`3`通过配置文件完成依赖注入

1）定义实体类

```java
package com.example.server.entity;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "person")//指向配置文件中的person属性
public class Person {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

2）配置文件

```yaml
person:
  name: zhanghuan
  age: 22
```

3）测试

```java
package com.example.server;

import com.example.server.entity.Person;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class ServerApplicationTests {
    @Autowired
    Person person;

    @Test
    void contextLoads() {
        System.out.println (person);//Person{name='zhanghuan', age=22}
    }

}

```

---

**集成测试**

1）导入依赖

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

2）创建测试类，并加上注解

```java
import domain.Account;
import mappers.AccountMapper;
import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

@RunWith (SpringRunner.class)
@SpringBootTest(classes = application.class)
public class test {
    @Autowired
    SqlSessionFactory factory;

    @Test
    public void test(){
        List<Account> list=factory.openSession ().getMapper (AccountMapper.class).findAll ();
        System.out.println (list);
    }
}
```

---

**整合redis**

1）导入依赖

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

2）配置文件中写入配置

```
spring.redis.host=127.0.0.1
sping.redis.port=6379
```

3）使用

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.BoundValueOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.test.context.junit4.SpringRunner;


@RunWith (SpringRunner.class)
@SpringBootTest(classes = application.class)
public class test {

    @Autowired
    RedisTemplate<String,String> redisTemplate;//注入redisTemplate

    @Test
    public void test(){

        BoundValueOperations provinces=redisTemplate.boundValueOps ("provinces");
        System.out.println (provinces.get ());

    }
}
```

---

**整合SpringDataJpa**

`1`依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId><!--需要mysql驱动支持-->
            <scope>runtime</scope>
        </dependency>
```

`2`配置文件

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql:///test2?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
    username: root
    password:
  jpa:
    database: mysql
    show-sql: true
    hibernate:
      ddl-auto: update
    generate-ddl: true

```

`3`实体对象

```java
package zhanghuan.jpa20200617.entity;

import javax.persistence.*;

@Entity
@Table(name = "role")
public class Role {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    @Column(name = "name")
    private String name;

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Role{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", users=" +
                '}';
    }
}
//注意：通过程序创建的表格，字段的编码不是utf8，需要自己去改编码
```

`4`接口

```java
package zhanghuan.jpa20200617.responsitory;

import org.springframework.data.jpa.repository.JpaRepository;
import zhanghuan.jpa20200617.entity.Role;

public interface RoleResponsitory extends JpaRepository<Role,Long> {
}

```

`5`测试

```java
package zhanghuan.jpa20200617;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import zhanghuan.jpa20200617.responsitory.RoleResponsitory;
import zhanghuan.jpa20200617.responsitory.UserResponsitory;



@SpringBootTest
class Jpa20200617ApplicationTests {

    @Autowired
    RoleResponsitory roleResponsitory;

    @Test
    void contextLoads() {
        System.out.println (roleResponsitory.findAll ());

    }

}

```

`6`注意问题

分页查询和以前不一样

```java
@RestController
public class SubjectController {
    @Autowired
    SubjectResponsitory subjectResponsitory;
    @GetMapping("/api/subject")
    public Result getSubjects(Pagination page){
        Pageable pageable=PageRequest.of (page.getPage ()-1,page.getLimit ());//构造Pageable对象用静态of方法，页码从0开始
        Page<Subject> page1= subjectResponsitory.findAll (pageable);
        List<Subject> list=page1.getContent ();
        System.out.println (list);
        return null;
    }
}
```





---

**idea快速创建Springboot项目**

`1`创建

1）选择创建Spring InitLiazr项目

2）填写group,artifact信息点击下一步

3）选择依赖，直至结束

| 依赖                 | 说明                                   |
| -------------------- | -------------------------------------- |
| spring web           | 基础包                                 |
| mysql driver         | 数据库驱动                             |
| mybatis framwork     | 持久层框架                             |
| lombok               | 实体类工具，使用前需要在idea上安装插件 |
| thymeleaf            | 模板引擎                               |
| spring boot devtools | 热部署                                 |

4）配置文件

application.properties文件是配置文件，必须配置数据库，否则项目无法运行

```
spring.application.name=demo
server.port=8080

spring.profiles.active=dev

spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test1?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=

#列名下划线转驼峰
mybatis.configuration.map-underscore-to-camel-case=true
#控制台打印sql
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

`2`实体类lombok辅助工具

在定义一个实体类，需要定义属性，及属性的setter、getter方法，构造方法、toString方法、hashcode方法，这些方法在实体类形成大量代码，不易阅读，所以用thymleaf辅助工具来简化实体类的创建使得代码的易读性提高

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;

@Builder//创建一个静态builder方法，调用该方法可直接创建该类的对象,如Book.builder().id(1).build()；对于jpa中的实体，使用该注解后要设置全参和无参的构造方法，否则会报错
@Data//创建setter方法、getter方法、toString方法、hashcode方法
@AllArgsConstructor//创建全参的构造方法
@NoArgsConstructor//创建无参的构造方法
public class Book {
    private int id;
    private String name;
    private double price;
    private int status;
    private Date createAt;

}
```

`3`thymeleaf模板

```java
package com.zhanghuan.demo.controller;

import com.zhanghuan.demo.entity.Book;
import com.zhanghuan.demo.mapper.BookMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.List;

@Controller
public class quickController {

    @Autowired
    BookMapper bookMapper;

    @GetMapping("/book")
    public String hello(Model model){
        Book book=bookMapper.findOne (1);
        model.addAttribute ("book",book);

        List<Book> books=bookMapper.findAll ();
        model.addAttribute ("booklist",books);

        boolean show=true;
        model.addAttribute ("show",show);

        String role="admin";
        model.addAttribute ("role",role);

        return "hello";//这里返回的是模板
    }
}

```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!--给标签赋值-->
    <p th:text="${book}"></p>

    <!--循环遍历-->
    <ul th:each="book: ${booklist}">
        <li  th:text="${book}"></li>
    </ul>

    <!--显示隐藏-->
    <p th:if="${show}==true">显示</p>

    <!--switch,case-->
    <div th:switch="${role}">
        <p th:case="'admin'">用户是管理员</p>
        <p th:case="'manager'">用户是经理</p>
        <p th:case="*">用户是别的玩意</p>
    </div>
</body>
</html>
```

`4`mybatis使用

1）定义mapper接口

```java
package com.zhanghuan.demo.mapper;

import com.zhanghuan.demo.entity.Book;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository//不加这个，在自动注入位置出现红色波浪线
public interface BookMapper {
    @Select ("SELECT * FROM book")
    List<Book> findAll();

    @Select ("SELECT * FROM book WHERE id=#{id}")
    Book findOne(@Param ("id") int id);//(@Param ("id")创建一个局部变量id，将接收过滤的参数赋值给这个变量，sql语句里就能拿到这个变量值
}

```

2）指定mapper包位置

```
@MapperScan("com.zhanghuan.demo.mapper")
```

启动类上加上这个注解，参数是mapper包的位置，spring会根据这个位置找到接口并创建代理对象放入容器

3）在controller中自动注入并使用

```java
package com.zhanghuan.demo.controller;

import com.zhanghuan.demo.entity.Book;
import com.zhanghuan.demo.mapper.BookMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.List;

@Controller
public class quickController {

    @Autowired
    BookMapper bookMapper;//将BookMapper的代理类注入

    @GetMapping("")
    public String hello(Model model){
        Book book=bookMapper.findOne (1);
        model.addAttribute ("book",book);

        List<Book> books=bookMapper.findAll ();
        model.addAttribute ("booklist",books);

        boolean show=true;
        model.addAttribute ("show",show);

        String role="admin";
        model.addAttribute ("role",role);

        return "hello";
    }
}

```

`5`请求的参数接收

1）获取get方式的数据

```java
package controller;

import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletRequest;



@RestController
public class quickController {

    @GetMapping("/detail")
    public String detail(HttpServletRequest request){//通过HttpServletRequest对象获取参数值
        System.out.println (request.getParameter ("name"));
        return "he";
    }

}
```

```java
package controller;

import org.springframework.web.bind.annotation.*;


@RestController
public class quickController {

    @GetMapping("/detail")
    public String detail(String name){
        System.out.println (name);//简化方式
        return "he";
    }

}
```

2）url路径中获取参数

```java
package controller;

import org.springframework.web.bind.annotation.*;


@RestController
public class quickController {

    @GetMapping("/detail/{bookId}")
    public String detail(@PathVariable("bookId") String bookId){
        System.out.println (bookId);
        return "he";
    }

}
```

3）post方式获取表单类型数据

```java
package controller;

import org.springframework.web.bind.annotation.*;



@RestController
public class quickController {

    @PostMapping("/book")
    public String book(String name){//还可以传入一个对象，自动将参数与对象的属性对应
        System.out.println (name);
        return "post";
    }

}
```

4）post方式获取json类型数据

```java
package controller;

import org.springframework.web.bind.annotation.*;



@RestController
public class quickController {

    @PostMapping("/book")
    public String book(@RequestBody String name){//@RequestBody表示从请求体中接收json数据，这里参数如果是bean，会自动封装
        System.out.println (name);
        return "post";
    }

}
```

`6`图书管理案例

项目目录

```
-config
	-CorsConfig.java
-controller
	-bookController.java
-entity
	-Book.java
-mapper
	-BookMapper.java
-util
	-Pagination.java
	-Result.java
DemoApplication.java
```

1）CorsConfig.java

```java
package com.zhanghuan.demo.config;
//跨域
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

@Configuration
public class CorsConfig{

    private CorsConfiguration buildConfig() {
        CorsConfiguration corsConfiguration = new CorsConfiguration ();
        corsConfiguration.addAllowedOrigin("*"); //允许任何域名
        corsConfiguration.addAllowedHeader("*"); //允许任何头
        corsConfiguration.addAllowedMethod("*"); //允许任何方法
        return corsConfiguration;
    }

    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", buildConfig()); //注册
        return  new CorsFilter (source);
    }

    }

```

2）bookController.java

```java
package com.zhanghuan.demo.controller;

import com.zhanghuan.demo.entity.Book;
import com.zhanghuan.demo.mapper.BookMapper;
import com.zhanghuan.demo.util.Pagination;
import com.zhanghuan.demo.util.Result;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@RestController
public class bookController {
    @Autowired
    BookMapper bookMapper;

    /*
    添加书
     */
    @PostMapping("/books")
    public Result<Object> addBook(@RequestBody Book book){
        book.setCreateAt (new Date ());
        try {
            bookMapper.insertOne (book);
            return Result.builder ().code("SUCCESS").data(book).build ();
        } catch (Exception e) {
            e.printStackTrace ( );
            return Result.builder ().code("FAILURE").message (e.getMessage ()).build ();
        }

    }

    /*
    获取所有书
     */
    @GetMapping("/books")
    public Result<Object> getBooks(String name, Pagination pg){
        pg.g_offset ();
        System.out.println (name);
        List<Book> books=bookMapper.findAll (name,pg);
        int total=bookMapper.countAll (name);
        pg.setTotal (total);
        Map<String,Object> map=new HashMap<> ();
        map.put("pagination",pg);
        map.put("books",books);
        Result<Object> result=Result.builder ().code ("SUCCESS").data (map).build ();
        return result;
    }

    /*
    根据id获取书
     */
    @GetMapping("/books/{bookId}")
    public Result<Object> getBookDetail(@PathVariable("bookId") int id){
        Book book=bookMapper.findOne (id);
        Result<Object> result=Result.builder ().code ("SUCCESS").data (book).build ();
        return result;
    }

    /*
    更新书
     */
    @PostMapping("/books/{bookId}")
    public Result<Object> updateBook(@PathVariable("bookId") int id,@RequestBody Book book ){
        book.setCreateAt (new Date ());
        try {
            bookMapper.updateOne (id,book);
            return Result.builder ().code("SUCCESS").build ();
        } catch (Exception e) {
            e.printStackTrace ( );
            return Result.builder ().code("FAILURE").message (e.getMessage ()).build ();
        }
    }

    /*
    删除书
     */
    @DeleteMapping("/books/{bookId}")
    public Result<Object> deleteBook(@PathVariable("bookId") int id){
        try {
            bookMapper.deleteOne (id);
            return Result.builder ().code("SUCCESS").build ();
        } catch (Exception e) {
            e.printStackTrace ( );
            return Result.builder ().code("FAILURE").message (e.getMessage ()).build ();
        }
    }
}

```

3）Book.java

```java
package com.zhanghuan.demo.entity;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Book {
    private int id;
    private String name;
    private String describe;
    private double price;
    private int status;
    @JsonFormat(parttern="yyyy-MM-dd")//设置日期格式
    private Date createAt;

}

```

4）BookMapper.java

```java
package com.zhanghuan.demo.mapper;

import com.zhanghuan.demo.entity.Book;
import com.zhanghuan.demo.util.Pagination;
import org.apache.ibatis.annotations.*;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface BookMapper {
    @Select("<script>" +
    "SELECT * FROM book"+
            "<where>"+
            "<if test='#{name} != null'>"+
            "AND name LIKE CONCAT('%',#{name},'%')"+
            "</if>"+
            "</where>"+
            " ORDER BY id ASC LIMIT #{pg.offset},#{pg.limit}"+
            "</script>")
    List<Book> findAll(@Param ("name") String name, @Param ("pg") Pagination pg);

    @Select ("SELECT COUNT(*) FROM book WHERE name LIKE CONCAT('%',#{name},'%')")
    int countAll(@Param ("name") String name);

    @Select("SELECT * FROM book WHERE id=#{id}")
    Book findOne(@Param("id") int id);

    @Insert("INSERT INTO book (`name`,price,`status`,`createAt`,`describe`) VALUES(#{book.name},#{book.price},#{book.status},#{book.createAt},#{book.describe})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    int insertOne(@Param ("book") Book book);

    @Update ("UPDATE book SET `name`=#{book.name},`price`=#{book.price},`status`=#{book.status},`createAt`=#{book.createAt},`describe`=#{book.describe} WHERE id=#{id}")//"UPDATE book SET `name`=#{book.name},`price`=#{book.price},`status`=#{book.status},`createAt`=#{book.createAt},`describe`=#{book.describe} WHERE id=#{bookid}"
    int updateOne(@Param("id") int id,@Param("book") Book book);

    @Delete ("DELETE FROM book WHERE id=#{id}")
    int deleteOne(@Param ("id") int id);
}


```

5）Pagination.java

```java
package com.zhanghuan.demo.util;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@AllArgsConstructor
@NoArgsConstructor
@Data
public class Pagination {
    private int page;
    private int limit;
    private int total;
    private int offset;
    public void g_offset(){
        this.offset=(this.page-1)*this.limit;
    }
}

```

6）Result.java

```java
package com.zhanghuan.demo.util;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Builder
@AllArgsConstructor
@NoArgsConstructor
@Data
public class Result<T> {
    private String code;
    private String message;
    private T data;
}

```

7）DemoApplication.java

```java
package com.zhanghuan.demo;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.zhanghuan.demo.mapper")
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run (DemoApplication.class, args);
    }

}

```

8）index.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
        integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/jquery@3.4.1/dist/jquery.slim.min.js"
        integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous">
    </script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js"
        integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous">
    </script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.4.1/dist/js/bootstrap.min.js"
        integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous">
    </script>
    <style>
        .pagination-text {
            border: none;
        }

        .pagination-text:hover {
            background-color: white;
        }

        .active_page {
            background-color: #dee2e6;
        }
    </style>
    <script>
        window.onload = function () {
            base_url = "http://localhost:8080"
            Vue.filter("formatTime_to_YMD", function (timeString) { //时间过滤器
                if (timeString != null) {
                    var date = new Date(timeString)
                    var year = date.getFullYear()
                    var month = date.getMonth() + 1
                    var date = date.getDate()
                    return year + '-' + month + '-' + date
                } else {
                    return ""
                }

            })
            Vue.filter("status_num_to_string", function (status) { //状态过滤器
                if (status == 1) {
                    return "启用"
                } else {
                    return "未启用"
                }
            })

            var app = new Vue({
                el: '#app',
                data: {
                    booklist: [],
                    pagination: [],
                    search_string: '',
                    total_page: '',
                    pages: [],
                    skip_num: '',
                    book: {
                        "name": '',
                        "price": '',
                        "describe": '',
                        "status": 1
                    }
                },
                watch: {
                    "pagination.limit": function () { //监视每页条数变量
                        this.getBooklist({
                            "page": this.pagination.page,
                            "limit": this.pagination.limit,
                            "name": ''
                        })
                    },
                    "skip_num": function () { //监视跳转框
                        var that = this
                        setTimeout(function () {
                            that.skip_to(that.skip_num)
                            that.skip_num = ""
                        }, 1000)
                    }
                },
                created() {
                    this.getBooklist({
                        "page": 1,
                        "limit": 5,
                        "name": ''
                    })
                },
                methods: {
                    //获取所有数据
                    getBooklist(dic) {
                        axios.get(base_url + "/books", {
                            params: dic
                        }).then(data => {
                            this.booklist = data.data.data.books
                            this.pagination = data.data.data.pagination
                            var total_page = Math.ceil(this.pagination.total / this.pagination
                                .limit)
                            this.total_page = total_page
                            //将页码生成并放置
                            this.pages = []
                            for (var i = 0; i < total_page; i++) {
                                this.pages.push(i + 1)
                            }
                            this.make_active_page()
                        })
                    },

                    //搜素
                    doSearch() {
                        this.getBooklist({
                            "page": 1,
                            "limit": this.pagination.limit,
                            "name": this.search_string
                        })
                    },

                    //清空搜素
                    clearSearch() {
                        this.search_string = ''
                        this.getBooklist({
                            "page": 1,
                            "limit": this.pagination.limit,
                            "name": this.search_string
                        })
                    },

                    //跳转
                    skip_to(page) {
                        if (page <= this.total_page && page > 0) {
                            this.pagination.page = page
                            this.getBooklist({
                                "page": page,
                                "limit": this.pagination.limit,
                                "name": this.search_string
                            })
                        }
                    },

                    //给页码加active样式
                    make_active_page(page) {
                        if (page == this.pagination.page) {
                            return "active_page"
                        }
                        return ""
                    },

                    //改变新增、编辑模态框标题
                    change_modal_header(id) { //将元素id传入，如果是0表示新增，否则表示编辑
                        var head = document.getElementById("exampleModalLabel")
                        head.setAttribute("book_id", id)
                        if (id == 0) {
                            head.innerHTML = "新增"
                            this.book = {
                                "name": '',
                                "price": '',
                                "describe": '',
                                "status": 1
                            }
                        } else {
                            head.innerHTML = "编辑"
                            axios.get(base_url + '/books/' + id).then(data => {
                                this.book.name = data.data.data.name
                                this.book.price = data.data.data.price
                                this.book.describe = data.data.data.describe
                            })

                        }

                    },

                    //改变删除模态框标题
                    change_delete_modal_header(id) {
                        var head = document.getElementById("deleteModalLabel")
                        head.setAttribute("book_id", id)
                    },

                    //新增和编辑
                    add_book() {
                        var head = document.getElementById("exampleModalLabel")
                        var book_id = head.getAttribute("book_id")
                        if (book_id == 0) {
                            axios.post(base_url + '/books', this.book).then(data => {
                                $('#myModal').modal('hide')
                                this.getBooklist({
                                    "page": this.pagination.page,
                                    "limit": this.pagination.limit,
                                    "name": ""
                                })
                            })
                        } else {
                            axios.post(base_url + '/books/' + book_id, this.book).then(data => {
                                $('#myModal').modal('hide')
                                this.getBooklist({
                                    "page": this.pagination.page,
                                    "limit": this.pagination.limit,
                                    "name": ""
                                })
                            })
                        }

                    },

                    //删除
                    delete_book() {
                        var head = document.getElementById("deleteModalLabel")
                        var book_id = head.getAttribute("book_id")
                        axios.delete(base_url + '/books/' + book_id).then(data => {
                            $('#exampleModal').modal('hide')
                            this.getBooklist({
                                "page": this.pagination.page,
                                "limit": this.pagination.limit,
                                "name": ""
                            })
                        })
                    }
                }

            })
        }
    </script>
</head>

<body>
    <div class="container" id="app">
        <p></p>
        <p>
            <span class="font-weight-bold" style="font-size: 2rem;">图书管理</span><button
                class="btn btn-primary float-right btn-lg" data-toggle="modal" data-target="#myModal"
                @click="change_modal_header(0)">+新增</button>
        </p>

        <!-- 搜素功能 -->
        <div class="card">
            <div class="card-body">
                <form class="form-inline">
                    <div class="form-group mx-sm-3 mb-2">
                        <label for="inputPassword2" class="sr-only">书名</label>
                        <input class="form-control" id="inputPassword2" placeholder="请输入书名" v-model="search_string">
                    </div>
                    <button type="button" class="btn btn-outline-dark  mb-2" @click="doSearch()">搜索</button>
                    &nbsp;&nbsp;
                    <button type="button" class="btn btn-outline-dark  mb-2" @click="clearSearch()">清空</button>
                </form>
            </div>
        </div>

        <p></p>

        <!-- 书的列表 -->
        <table class="table table-borderless">
            <thead>
                <tr>
                    <th scope="col">编号</th>
                    <th scope="col">书名</th>
                    <th scope="col">单价</th>
                    <th scope="col">状态</th>
                    <th scope="col">新增时间</th>
                    <th scope="col">操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item of booklist">
                    <th scope="row" v-text="item.id">1</th>
                    <td v-text="item.name">Mark</td>
                    <td v-text="item.price">Otto</td>
                    <td>{{item.status|status_num_to_string}}</td>
                    <td>{{item.createAt|formatTime_to_YMD}}</td>
                    <td v-bind:id="item.id">
                        <a class="text-info" style="cursor: pointer;" data-toggle="modal" data-target="#myModal"
                            @click="change_modal_header(item.id)">编辑</a>
                        &nbsp;&nbsp;
                        <a class="text-info" style="cursor: pointer;" data-toggle="modal" data-target="#exampleModal"
                            @click="change_delete_modal_header(item.id)">删除</a>
                    </td>
                </tr>
            </tbody>
        </table>

        <!-- 底部页码部分 -->
        <nav aria-label="Page navigation example">
            <ul class="pagination float-right">
                <li class="page-item"><a class="page-link pagination-text">共{{pagination.total}}条</a></li>
                &nbsp;&nbsp;
                <li class="page-item"><a class="page-link" @click="skip_to(pagination.page-1)">&lt;</a></li>
                <li class="page-item" v-for="item in pages"><a class="page-link page"
                        v-bind:class="make_active_page(item)" @click="skip_to(item)">{{item}}</a></li>
                <li class="page-item"><a class="page-link" @click="skip_to(pagination.page+1)">&gt;</a></li>
                &nbsp;&nbsp;
                <select v-model="pagination.limit">
                    <option value="5">每页5条</option>
                    <option value="10">每页10条</option>
                    <option value="15">每页15条</option>
                    <option value="20">每页20条</option>
                </select>

                &nbsp;&nbsp;&nbsp;&nbsp;
                <li class="page-item"><a class="page-link pagination-text">跳至</a></li>
                <input type="text" class="inline-block" style="width: 4rem;" v-model="skip_num">
                &nbsp;
                <li class="page-item"><a class="page-link pagination-text">页</a></li>
            </ul>
        </nav>


        <!-- 新增、编辑模态框 -->
        <div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel"
            aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">New message</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <form>
                            <div class="form-group">
                                <label for="recipient-name" class="col-form-label">书名:</label>
                                <input type="text" class="form-control" v-model="book.name">
                            </div>
                            <div class="form-group">
                                <label for="recipient-name" class="col-form-label">价格:</label>
                                <input type="number" class="form-control" v-model="book.price">
                            </div>
                            <div class="form-group">
                                <label for="message-text" class="col-form-label">描述:</label>
                                <textarea class="form-control" id="message-text" v-model="book.describe"></textarea>
                            </div>
                            <p>状态:</p>
                            <div class="form-group">
                                <label class="radio-inline">
                                    <input type="radio" value="1" v-model="book.status" checked>开启
                                </label>
                                &nbsp;&nbsp;
                                <label class="radio-inline">
                                    <input type="radio" value="0" v-model="book.status" name="status">未启用
                                </label>
                            </div>
                        </form>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
                        <button type="button" class="btn btn-primary" @click="add_book()">确定</button>
                    </div>
                </div>
            </div>
        </div>


        <!-- 删除模态框 -->
        <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel"
            aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="deleteModalLabel">删除</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        是否确定要删除该条记录？
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
                        <button type="button" class="btn btn-primary" @click="delete_book()">确定</button>
                    </div>
                </div>
            </div>
        </div>

    </div>
</body>

</html>
```

`7`参数验证

对于前台传过来的参数进行合法性验证

1）在要验证的属性上标记Validated注解，表示该属性指向的对象在实例化时按照其规则进行验证，如果验证不通过，就抛出异常。比如，这里在控制器方法的参数加上Validated注解

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
```



```java
package com.zhanghuan.loginapi.controller;

import com.zhanghuan.loginapi.entity.User;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class testController {
    @GetMapping("/aa")
    public String aa(@Validated User user){//表示要对user对象进行验证
        System .out.println (user);
        return "get the mesage";
    }
}

```

2）在实体类的属性上用注解标记验证规则

```java
package com.zhanghuan.loginapi.entity;


import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.validation.constraints.NotBlank;//这个需要导入依赖，非springboot自带
import java.util.Date;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class User {
    private int id;
    
    @NotBlank(message = "不可以为空")//在属性上标注验证规则，message表示提示信息
    private  String mobile;
    
    private String nickname;
    private String password;
    private  String avatarUrl;
    private Date createAt;
}
```

验证标记注解及其验证内容

| 注解      | 验证内容                                                 |
| --------- | -------------------------------------------------------- |
| @NotNull  | 不可为Null                                               |
| @NotEmpty | 不可为空字符串                                           |
| @NotBlank | 不可为Null，不可为空字符串，只可作用在字符串上，否则报错 |
| @Email    | 验证邮箱                                                 |
| @Min(5)   | 最小为5，括号里参数自定义                                |
| @Max (10) | 最大为10，括号里参数自定义                               |

3）BindingResult捕获异常以获取校验失败原因

```java
package com.zhanghuan.loginapi.controller;

import com.zhanghuan.loginapi.entity.User;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class testController {
    @GetMapping("/aa")
    public String aa(@Validated User user, BindingResult bindingResult){//通过BindingResult来捕获验证失败抛出的异常
        System .out.println (user);//User(id=0, mobile=, nickname=null, password=123456, avatarUrl=null, createAt=null)
        return bindingResult.getFieldError ().getDefaultMessage ();//不能为空
    }
}

```

4）统一异常处理

(1)定义一个类，加上@ControllerAdvice

(2)定义异常处理方法，加上@ExceptionHandler(Throwable.class)，@ResponseBody

```java
package com.zhanghuan.loginapi.ExceptionHander;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

import java.net.BindException;

@ControllerAdvice//表示控制器发生的异常都会送到这里来
public class valicadHandler {
    @ExceptionHandler(Throwable.class)//Throwable的继承类都会被捕获
    @ResponseBody//捕获异常后向客户端返回字符串数据
    public String exceptionHandler(Throwable e){//通过参数e来接收异常
        return e.getMessage ();
    }

}

```

在这里捕获到异常判断是否是MethodArgumentNotValidException的实现类，进行处理后返回

`8`拦截器

1）定义一个类实现HandlerInterceptor接口，并重写preHandle方法

```java
package com.zhanghuan.loginapi.interceptor;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class loginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println ("are you ok");
        return true;
    }
}

```

2）定义配置文件类实现WebMvcConfigurer接口，并重写addInterceptors方法

```java
package com.zhanghuan.loginapi.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.config.annotation.InterceptorRegistration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class addInterceptorConfig implements WebMvcConfigurer {
    @Autowired
    HandlerInterceptor interceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        InterceptorRegistration ir=registry.addInterceptor (interceptor);
        ir.addPathPatterns ("/api/**").excludePathPatterns ("/api/user/create","/api/token/create","/api/sms");
    }
}

```

---

**登陆注册案例**

前后端分离实现登陆注册

`1`表设计

1）用户表user

| 字段      | 类型   | 说明     |
| --------- | ------ | -------- |
| id        | int    |          |
| mobile    | String | 手机号   |
| nickname  | String | 昵称     |
| password  | String | 密码     |
| avatarUrl | String | 头像     |
| createAt  | Date   | 创建时间 |

2）短信表sms

| 字段             | 类型   | 说明           |
| ---------------- | ------ | -------------- |
| id               | int    |                |
| mobile           | String | 手机号         |
| verificationKey  | String | 随机32位字符串 |
| verificationCode | String | 4位验证码      |
| createAt         | Date   | 创建时间       |

3）令牌表token

| 字段     | 类型   | 说明              |
| -------- | ------ | ----------------- |
| id       | int    |                   |
| token    | String | 随机32位字符串    |
| user_id  | String | 用户id            |
| createAt | Date   | 创建时间          |
| endTime  | Date   | token生效结束时间 |

`2`接口设计

| 功能     | 发送短信验证码                                               |
| -------- | ------------------------------------------------------------ |
| url      | /api/sms                                                     |
| method   | POST                                                         |
| 参数     | {"mobile":""}                                                |
| 成功结果 | {"code": "SUCCESS", "data": {"verificationKey": "xxx"}, "message": null} |
| 失败结果 | {"code": "ERROR", "message": "失败原因", "data": null}       |



| 功能     | 使用手机号码注册（创建用户）                                 |
| -------- | ------------------------------------------------------------ |
| url      | /api/user/create                                             |
| method   | POST                                                         |
| 参数     | {"mobile":"","nickname":"","password":"","verificationKey":"","verificationCode":""} |
| 成功结果 | {"code": "SUCCESS", "data": {"token":"xxx"}, "message": null} |
| 失败结果 | {"code": "ERROR", "message": "失败原因", "data": null}       |



| 功能     | 获取个⼈信息（验证是否登陆）                                 |
| -------- | ------------------------------------------------------------ |
| url      | /api/user/whoami                                             |
| method   | GET                                                          |
| 参数     | {"token":""}                                                 |
| 成功结果 | {<br/>"code": "SUCCESS",<br/>"data": {<br/>"mobile": "⼿机号",<br/>"nickname": "昵称",<br/>"avatarUrl": "头像url，以http开头"<br/>},<br/>"message": null<br/>} |
| 失败结果 | {"code": "ERROR", "message": "失败原因", "data": null}       |



| 功能     | 登录(并创建token)                                            |
| -------- | ------------------------------------------------------------ |
| url      | /api/token/create                                            |
| method   | POST                                                         |
| 参数     | {"mobile":"","password":""}                                  |
| 成功结果 | {"code": "SUCCESS", "data": {"token":"xxx"}, "message": null} |
| 失败结果 | {"code": "ERROR", "message": "失败原因", "data": null}       |



`3`后端代码

1）目录结构

```
//application同级目录
-config
	addInterceptorConfig.java
	CorsConfig.java
-controller
	loginController.java
-dto
	createUser.java
	Phone.java
	Result.java
	userContex.java
-entity
	Sms.java
	Token.java
	User.java
-ExceptionHandler
	valicadHandler.java
-interceptor
	loginInterceptor.java
-mapper
	smsMapper.java
	tokenMapper.java
	userMapper.java
-service
	makeToken.java
	smsService.java
	UserService.java
LoginapiApplication.java
```

2）各文件

(1)addInterceptorConfig.java

```java
package com.zhanghuan.loginapi.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.config.annotation.InterceptorRegistration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class addInterceptorConfig implements WebMvcConfigurer {
    @Autowired
    HandlerInterceptor interceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        InterceptorRegistration ir=registry.addInterceptor (interceptor);
        ir.addPathPatterns ("/api/**").excludePathPatterns ("/api/user/create","/api/token/create","/api/sms","/api/user/whoami");
    }
}

```



(2)CorsConfig.java

```java
package com.zhanghuan.loginapi.config;

//跨域
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

@Configuration
public class CorsConfig{

    private CorsConfiguration buildConfig() {
        CorsConfiguration corsConfiguration = new CorsConfiguration ();
        corsConfiguration.addAllowedOrigin("*"); //允许任何域名
        corsConfiguration.addAllowedHeader("*"); //允许任何头
        corsConfiguration.addAllowedMethod("*"); //允许任何方法
        return corsConfiguration;
    }

    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", buildConfig()); //注册
        return  new CorsFilter (source);
    }

}
```



(3)loginController.java

```java
package com.zhanghuan.loginapi.controller;

import com.zhanghuan.loginapi.dto.Phone;
import com.zhanghuan.loginapi.dto.Result;
import com.zhanghuan.loginapi.dto.createUser;
import com.zhanghuan.loginapi.dto.userContex;
import com.zhanghuan.loginapi.entity.Sms;
import com.zhanghuan.loginapi.entity.User;
import com.zhanghuan.loginapi.mapper.userMapper;
import com.zhanghuan.loginapi.service.UserService;
import com.zhanghuan.loginapi.service.makeToken;
import com.zhanghuan.loginapi.service.smsService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.Map;

@RestController
public class loginController {

    @Autowired
    userMapper userdao;

    @Autowired
    makeToken makeToken;

    @Autowired
    smsService smsdao;

    @Autowired
    UserService userService;

    @Autowired
    smsService smsService;

    @PostMapping("/api/token/create")
    public Result<Object> login(@RequestBody @Validated User loginUser){
        User user=userdao.findByMobie (loginUser.getMobile ());
        Result<Object> result=null;
        if(user!=null){
            if(user.getPassword ().equals (loginUser.getPassword ())){
                String token_str=makeToken.getToken (user.getId ());
                Map<String,String> data=new HashMap<> ();
                data.put ("token",token_str);
                result=Result.builder ().code ("SUCCESS").data (data).build ();
            }else {
                result=Result.builder ().code ("FAILLURE").message ("密码错误").build ();
                return result;
            }
        }else {
            result=Result.builder ().code ("FAILLURE").message ("该用户不存在").build ();
        }
        return result;
    }

    @PostMapping("/api/user/create")
    public Result<Object> createUser(@RequestBody @Validated createUser createUser){
        Result<Object> result=null;
        String verificationKey=createUser.getVerificationKey ();
        String verificationCode=createUser.getVerificationCode ();
        Sms sms=smsService.findSmsByVerificationCode (verificationKey);
        boolean isVerify=sms.getVerificationCode ().equals (verificationCode)&&sms.getMobile ().equals (createUser.getMobile ());
        if(isVerify){//验证码是否正确
            User user=userService.creteUser (createUser);
            if(user!=null){
                String token=makeToken.getToken (user.getId ());
                Map<String,String> map=new HashMap<> ();
                map.put ("token",token);
                result=Result.builder ().code ("SUCCESS").data (map).build ();
            }else{
                result=Result.builder ().code ("FAILLURE").message ("手机号已存在").build ();
            }
        }else {
            result=Result.builder ().code ("FAILLURE").message ("验证码错误").build ();
        }


        return result;
    }

    @PostMapping("/api/sms")
    public Result<Object> getSms(@RequestBody Phone phone){
        Result<Object> result=null;
        String verificationKey=smsdao.getSms (phone.getNumber ());
        Map<String,String> map=new HashMap<> ();
        map.put("verificationKey",verificationKey);
        result=Result.builder ().code ("SUCCESS").data (map).build ();
        return result;
    }

    @GetMapping("/api/user/whoami")
    public Result<Object> whoami(String token){
        User user=makeToken.getUser (token);
        Result<Object> result=null;
        if(user!=null){
            result=Result.builder ().code ("SUCCESS").data (user).build ();
        }else {
            result=Result.builder ().code ("FAILLURE").message ("token已过期或不存在").build ();
            userContex.clear ();//清除掉用户
        }
        return result;
    }


}

```



(4)createUser.java

```java
package com.zhanghuan.loginapi.dto;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.validation.constraints.NotBlank;

@Builder
@AllArgsConstructor
@NoArgsConstructor
@Data
public class createUser {

    @NotBlank(message = "手机号不可以为空")
    private String mobile;

    @NotBlank(message = "昵称不可以为空")
    private String nickname;

    @NotBlank(message = "密码不可以为空")
    private String password;

    @NotBlank(message = "verificationKey不可以为空")
    private String verificationKey;

    @NotBlank(message = "验证码不可以为空")
    private String verificationCode;
}

```



(5)Phone.java

```java
package com.zhanghuan.loginapi.dto;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.validation.constraints.NotBlank;

@Builder
@AllArgsConstructor
@NoArgsConstructor
@Data
public class Phone {
    @NotBlank(message = "手机号不可以为空")
    private String number;
}

```



(6)Result.java

```java
package com.zhanghuan.loginapi.dto;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Builder
@AllArgsConstructor
@NoArgsConstructor
@Data
public class Result<T> {
    private String code;
    private String message;
    private T data;
}
```



(7)userContex.java

```java
package com.zhanghuan.loginapi.dto;

import com.zhanghuan.loginapi.entity.User;

public class userContex {
    //存储user对象的容器
    private static final ThreadLocal<User> current=new ThreadLocal<> ();

    public static void setUser(User user){
        current.set (user);
    }
    public static User getUser(){
        return current.get ();
    }
    public static void clear(){
        current.remove ();
    }
}

```

(8)Sms.java

```java
package com.zhanghuan.loginapi.entity;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class Sms {
    private int id;
    private String mobile;
    private String verificationKey;
    private String verificationCode;
    private Date createAt;
}
```



(9)Token.java

```java
package com.zhanghuan.loginapi.entity;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class Token {
    private int id;
    private String token;
    private int userId;
    private Date createAt;
    private Date endTime;
}

```



(10)User.java

```java
package com.zhanghuan.loginapi.entity;


import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.validation.constraints.*;
import java.util.Date;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class User {
    private int id;

    @NotBlank(message = "手机号不可以为空")
    private  String mobile;

    private String nickname;

    @NotBlank(message = "密码不可以为空")
    private String password;

    private  String avatarUrl;

    private Date createAt;
}

```



(11)valicadHandler.java

```java
package com.zhanghuan.loginapi.ExceptionHandler;

import com.zhanghuan.loginapi.dto.Result;
import org.springframework.validation.ObjectError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.ArrayList;
import java.util.List;


@ControllerAdvice
public class valicadHandler {
    @ExceptionHandler(Throwable.class)
    @ResponseBody
    public Result exceptionHandler(Throwable e){
        Result<Object> result=null;
        if(e instanceof MethodArgumentNotValidException){
            List<ObjectError> erros=((MethodArgumentNotValidException) e).getBindingResult ().getAllErrors ();
            String e_str=handleErrors (erros);
            result=Result.builder ().code ("FAILLURE").message (e_str).build ();
        }else{
            result=Result.builder ().code ("FAILLURE").message (e.getMessage ()).build ();
        }
        return result;
    }


    private String handleErrors(List<ObjectError> erros){
        List<String> list=new ArrayList<> ();
        for (ObjectError item:erros
             ) {
            list.add (item.getDefaultMessage ());
        }
        return list.toString ().replace ("[","").replace ("]","");
    }



}

```



(12)loginInterceptor.java

```java
package com.zhanghuan.loginapi.interceptor;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import com.zhanghuan.loginapi.dto.userContex;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class loginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        if(userContex.getUser ()==null){
            throw new RuntimeException ("用户未登录");
        }else {
            return true;
        }

    }
}
```



(13)smsMapper.java

```java
package com.zhanghuan.loginapi.mapper;

import com.zhanghuan.loginapi.entity.Sms;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

@Repository
public interface smsMapper {
    @Insert ("INSERT INTO sms (mobile,verificationKey,verificationCode,createAt) VALUES (#{sms.mobile},#{sms.verificationKey},#{sms.verificationCode},#{sms.createAt})")
    void insertOne(@Param ("sms") Sms sms);

    @Select ("SELECT * FROM sms WHERE verificationKey=#{key}")
    Sms findByVerificationKey(String key);
}

```



(14)tokenMapper.java

```java
package com.zhanghuan.loginapi.mapper;

import com.zhanghuan.loginapi.entity.Token;
import org.apache.ibatis.annotations.*;
import org.springframework.stereotype.Repository;

@Repository
public interface tokenMapper {
    @Insert ("INSERT INTO token (token,userId,createAt,endTime) Values (#{token.token},#{token.userId},#{token.createAt},#{token.endTime})")
    int insertOne(@Param ("token")Token token);

    @Update ("UPDATE token set endTime=#{token.endTime} WHERE token=#{token.token}")
    void updateToken_endTime(@Param ("token")Token token);

    @Select ("SELECT * FROM token WHERE token=#{token_str}")
    Token findOne(String token_str);
}

```



(15)userMapper.java

```java
package com.zhanghuan.loginapi.mapper;

import com.zhanghuan.loginapi.entity.User;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Options;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

@Repository
public interface userMapper {
    @Select ("SELECT * FROM user WHERE mobile=#{mobile}")
    User findByMobie(String mobile);

    @Select ("SELECT * FROM user WHERE id=#{id}")
    User findById(int id);

    @Insert ("INSERT INTO user (mobile,nickname,password,avatarUrl,createAt) VALUES (#{user.mobile},#{user.nickname},#{user.password},#{user.avatarUrl},#{user.createAt})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    void indertOne(@Param ("user") User user);
}

```



(16)makeToken.java

```java
package com.zhanghuan.loginapi.service;

import com.zhanghuan.loginapi.dto.userContex;
import com.zhanghuan.loginapi.entity.Token;
import com.zhanghuan.loginapi.entity.User;
import com.zhanghuan.loginapi.mapper.tokenMapper;

import com.zhanghuan.loginapi.mapper.userMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.UUID;

@Component
public class makeToken {
    @Autowired
    tokenMapper tokendao;

    @Autowired
    userMapper userdao;

    public String getToken(int user_id){//为用户创建token
        String token_str= UUID.randomUUID ().toString ().replace ("-","");
        Date date=new Date ();
        Date endTime=new Date (date.getTime ()+60*30*1000);//30分钟过期
        Token token=Token.builder ().token (token_str).userId (user_id).createAt (date).endTime (endTime).build ();

        tokendao.insertOne (token);
        return token_str;
    }
    public void updateToken_endTime(String token_str){//更新token过期时间
        Date date=new Date ();
        Date endTime=new Date (date.getTime ()+60*30*1000);
        Token token=Token.builder ().token (token_str).endTime (endTime).build ();
        tokendao.updateToken_endTime (token);
    }
    public User getUser(String token_str){//通过token获取用户
        Token token=tokendao.findOne (token_str);
        User user=null;
        Date now=new Date ();
        Date endtime=token.getEndTime ();
        System.out.println (now);
        System.out.println (endtime);
        if(now.getTime ()<endtime.getTime ()){
            user=userdao.findById (token.getUserId ());
        }
        if(user!=null){
            updateToken_endTime (token_str);
            if(userContex.getUser ()==null){
                userContex.setUser (user);//将user存入
            }
        }

        return user;
    }
}

```



(17)smsService.java

```java
package com.zhanghuan.loginapi.service;

import com.zhanghuan.loginapi.entity.Sms;
import com.zhanghuan.loginapi.mapper.smsMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.UUID;

@Component
public class smsService {
    @Autowired
    smsMapper smsdao;

    public String getSms(String mobile){//获取短信
        String verificationCode=String.valueOf ((int)((Math.random()*9+1)*1000));
        String verificationKey= UUID.randomUUID ().toString ().replace ("-","");
        Sms sms=Sms.builder ().mobile (mobile).verificationCode (verificationCode).verificationKey (verificationKey).createAt (new Date ()).build ();
        smsdao.insertOne (sms);
        System.out.println ("手机验证码是："+verificationCode);
        return verificationKey;
    }
    public Sms findSmsByVerificationCode(String verificationKey){
        Sms sms=smsdao.findByVerificationKey (verificationKey);
        return sms;
    }
}

```



(18)UserService.java

```java
package com.zhanghuan.loginapi.service;

import com.zhanghuan.loginapi.dto.createUser;
import com.zhanghuan.loginapi.entity.User;
import com.zhanghuan.loginapi.mapper.userMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.Date;

@Component
public class UserService {

    @Autowired
    userMapper userdao;


    public User creteUser(createUser createUser){//新建用户
        String mobile=createUser.getMobile ();
        String nickname=createUser.getNickname ();
        String password=createUser.getPassword ();

        User user=null;

        if(findUserByPhone (mobile)==null){
            int random_num=(int)Math.ceil (Math.random ()*5);
            String avatarUrl="http://localhost:8080/headimg/"+random_num+".jpg";
            user=User.builder ().mobile (mobile).nickname (nickname).avatarUrl (avatarUrl).password (password).createAt (new Date ()).build ();
            userdao.indertOne (user);
        }

        return user;
    }
    public User findUserByPhone(String number){
        User user=null;
        user=userdao.findByMobie (number);
        return user;
    }
}

```



(19)LoginapiApplication.java

```java
package com.zhanghuan.loginapi;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.zhanghuan.loginapi.mapper")
public class LoginapiApplication {

    public static void main(String[] args) {
        SpringApplication.run (LoginapiApplication.class, args);
    }

}

```

`4`前端代码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
        integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/jquery@3.4.1/dist/jquery.slim.min.js"
        integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous">
    </script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js"
        integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous">
    </script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.4.1/dist/js/bootstrap.min.js"
        integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous">
    </script>
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>

</head>

<body>
    <div class="container">
        <p>&nbsp;</p>
        <p>&nbsp;</p>
        <p>&nbsp;</p>
        <p>&nbsp;</p>
        <div class="row">
            <div class="col-md-4"></div>
            <div class="clo-md-4" id="staggered-list-demo">
                <transition mode='out-in'>
                    <!--动画效果-->
                    <router-view></router-view>
                    <!--组件会按照路由规则渲染到这里面-->
                </transition>
            </div>
        </div>
        <!--创建用户-->
        <template id="createuser">
            <form>
                <div class="form-group">
                    <label for="mobie">手机号</label>
                    <input type="text" v-model="phone_number" class="form-control" id="mobie">
                </div>
                <div class="form-group">
                    <label for="nickname">昵称</label>
                    <input type="text" v-model="nickname" class="form-control" id="nickname">
                </div>
                <div class="form-group">
                    <label for="exampleInputPassword1">密码</label>
                    <input type="password" v-model="password" class="form-control" id="exampleInputPassword1">
                </div>
                <div class="form-group">
                    <label for="verificationCode">验证码</label>
                    <input type="text" v-model="verificationCode" class="form-control" id="verificationCode">
                </div>
                <button type="button" @click="getverificationCode()" class="btn btn-sm btn-light">发送验证码</button>
                <p id="cretemsg" class="text-center text-info"></p>
                <p class="text-center">
                    <button type="button" @click="docreateuser()" class="btn btn-lg btn-outline-primary">提交</button>
                </p>
                <router-link to='/login' tag='p' class="text-primary text-center" style="cursor: pointer;"><u>返回登陆界面</u>
                </router-link>
            </form>
        </template>

        <!--登陆-->
        <template id="login">
            <form>
                <div class="form-group">
                    <label for="exampleInputEmail1">手机号</label>
                    <input type="email" v-model="mobile" class="form-control" id="exampleInputEmail1"
                        aria-describedby="emailHelp">
                </div>
                <div class="form-group">
                    <label for="exampleInputPassword1">密码</label>
                    <input type="password" v-model="password" class="form-control" id="exampleInputPassword1">
                </div>
                <p class="text-center text-info" id="loginmsg"></p>
                <p>
                    <button @click="do_login()" type="button" class="btn btn-outline-primary float-left">登陆</button>
                    <router-link to='/assign' tag='button' class="btn btn-outline-secondary float-right">注册
                    </router-link>
                </p>
                <p>
                    <a>&nbsp;</a>
                </p>
                <p>
                    <a>&nbsp;</a>
                </p>
                <p @click="skip_index" class="text-primary text-center" style="cursor: pointer;"><u>去主页看看</u></p>
            </form>
        </template>
    </div>

    <!--主页-->
    <template id="index">
        <div class="card">
            <div class="card-header">
                <img v-bind:src="user.avatarUrl" alt="" style="width: 3rem;">
            </div>

            <div class="card-body">
                <p>昵称：<span v-text="user.nickname"></span></p>
                <p>手机号：<span v-text="user.mobile"></span></p>
                <button @click="layout()" type="button" class="btn btn-outline-primary">退出登陆</button>
            </div>
        </div>
    </template>
</body>
<script>
    const base_dir = "http://localhost:8080/"
    var hub = new Vue()
    const login = {
        template: '#login',
        data: function () {
            return {
                mobile: "",
                password: ""
            }
        },
        methods: {
            do_login() {//登陆
                let that = this
                axios.post(base_dir + "/api/token/create", {
                    "mobile": this.mobile,
                    "password": this.password
                }).then(data => {
                    let code = data.data.code
                    let dt = data.data.data
                    let message = data.data.message
                    console.log(data)
                    if (code == "SUCCESS") {
                        localStorage.setItem("token", dt.token)
                        document.getElementById("loginmsg").innerHTML = ""
                        that.skip_index()
                    } else {
                        document.getElementById("loginmsg").innerHTML = message
                    }
                })
            },
            skip_index() {//跳转到主页
                if (localStorage.getItem("token")) {
                    hub.$emit('islogin')
                    this.$router.push("index")
                } else {
                    document.getElementById("loginmsg").innerHTML = "请先进行登陆"
                }

            }
        },

    }

    const assign = {
        template: '#createuser',
        data: function () {
            return {
                phone_number: "",
                nickname: "",
                password: "",
                verificationKey: "",
                verificationCode: ""
            }
        },
        methods: {
            getverificationCode() {//获取验证码
                if (this.isPoneAvailable(this.phone_number)) {
                    axios.post(base_dir + "/api/sms", {
                        "number": this.phone_number
                    }).then(data => {
                        console.log(data)
                        this.verificationKey = data.data.data.verificationKey
                        document.getElementById("cretemsg").innerHTML = "验证码已发送，请注意查看手机"
                    })
                } else {
                    document.getElementById("cretemsg").innerHTML = "手机号不正确"
                }

            },
            docreateuser() {//创建用户提交
                if (this.isPoneAvailable(this.phone_number) == false) {
                    document.getElementById("cretemsg").innerHTML = "手机号不正确"
                    return
                }
                let that = this
                if (this.verificationCode != "" && this.verificationKey != "") {
                    let obj = {
                        "mobile": this.phone_number,
                        "nickname": this.nickname,
                        "password": this.password,
                        "verificationKey": this.verificationKey,
                        "verificationCode": this.verificationCode
                    }
                    axios.post(base_dir + "/api/user/create", obj).then(data => {
                        console.log(data)
                        if (data.data.code == "SUCCESS") {
                            localStorage.setItem("token", data.data.data.token)
                            that.skip_index()
                        } else {
                            document.getElementById("cretemsg").innerHTML = data.data.message
                        }
                    })
                } else {
                    document.getElementById("cretemsg").innerHTML = "验证码不正确"
                }
            },
            isPoneAvailable(pone) {//判断手机号是否合法
                var myreg = /^[1][3,4,5,7,8][0-9]{9}$/;
                if (!myreg.test(pone)) {
                    return false;
                } else {
                    if (pone.length == 11) {
                        return true
                    }
                    return false;
                }
            },
            skip_index() {//跳转到主页
                if (localStorage.getItem("token")) {
                    hub.$emit('islogin')
                    this.$router.push("index")
                } else {
                    document.getElementById("loginmsg").innerHTML = "请先进行登陆"
                }

            }
        }
    }

    const index = {
        template: "#index",
        data: function () {
            return {
                user: ""
            }
        },
        mounted() {
            hub.$on('islogin', () => {
                console.log("事件監聽到")
                this.getuser()
            })
            this.getuser()

        },
        methods: {
            layout() {//退出登陆
                localStorage.clear('user')
                localStorage.clear('token')
                this.$router.push('login')
            },
            getuser() {//验证用户合法性并获取用户信息
                let token = localStorage.getItem("token")
                console.log(this.user, token)
                if (token != null) {
                    axios.get(base_dir + "/api/user/whoami?token=" + token).then(data => {
                        console.log(data)
                        console.log("this is whoami")
                        let code = data.data.code
                        let dt = data.data.data
                        let message = data.data.message
                        if (code == "SUCCESS") {

                            localStorage.setItem('user', JSON.stringify(dt))
                            this.user = JSON.parse(localStorage.getItem('user'))

                        } else {
                            localStorage.removeItem('token')
                            localStorage.removeItem('user')
                            that.$router.push("login")
                        }
                    })

                    console.log(this.user)
                } else {
                    this.$router.push('login')
                }
            }
        },

    }

    const routes = [{
            path: '/',
            redirect: '/index'
        }, //默认跳转到主页
        {
            path: '/login',
            component: login
        },
        {
            path: '/assign',
            component: assign
        },
        {
            path: '/index',
            component: index
        },
    ]
    var router_obj = new VueRouter({
        routes,
    })
    var app = new Vue({

        el: '#staggered-list-demo',
        router: router_obj,


    })
</script>

</html>
```

---

**异步方法**

```java
import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncResult;
import org.springframework.stereotype.Component;

import java.io.FileOutputStream;
import java.io.IOException;
import java.util.concurrent.Future;

@Component
public class LogUtil {
    @Async
    public Future<String> log() throws InterruptedException, IOException {
        System.out.println("异步方法开始执行");
        Thread.sleep(5000);
        XSSFWorkbook workbook=new XSSFWorkbook();
        XSSFSheet sheet=workbook.createSheet();
        XSSFRow row=sheet.createRow(0);
        row.createCell(0).setCellValue("are you ok");

        FileOutputStream fileOutputStream=new FileOutputStream("C:\\Users\\admin\\Desktop\\测试一下.xlsx");
        workbook.write(fileOutputStream);
        System.out.println("异步方法执行结束");
        return new AsyncResult<String>("执行完成了");
    }
}

```

3）执行

```java
    @Test
    public void test1() throws InterruptedException, IOException, ExecutionException {
        int i=1;
        Future<String> future=logUtil.log();//执行异步方法，定义变量接受返回值
        System.out.println(i);
        System.out.println(i);
        System.out.println(i);
        while (true){
            if(future.isDone()){
                System.out.println(future.get());//获取异步方法的返回值
                break;
            }
        }
        /*结果
            2
            2
            2
            异步方法开始执行
            异步方法执行结束
            执行完成了
         */

    }
```


---
layout: post
title: SpringMvc
tags: java
categories: backends
---

概览、快速开始、组成结构、请求、响应、ssm整合

**概览**

`1`服务器三层框架

表现层	springmvc

业务层	sping

持久层	mybatis

`2`mvc对应

model	javabean

view	jsp/html

controller	servlet

`3`springmvc和struts2对比

共同点：

都是表现层框架，基于mvc模型

底层离不开servletAPI

处理请求的机制都是一个核心控制器

不同点：

|          | springmvc    | struts2    |
| -------- | ------------ | ---------- |
| 入口     | servlet      | filter     |
| 设计     | 基于方法设计 | 基于类设计 |
| 开发效率 | 一般         | 稍高       |

---

**快速开始**

`1`pom中导入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>cn.itcast</groupId>
  <artifactId>springmvc_day01_01_start</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>springmvc_day01_01_start Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.2.RELEASE</spring.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>

  </dependencies>

  <build>
    <finalName>springmvc_day01_01_start</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.0.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.7.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.20.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
      </plugin>
    </plugins>
  </build>
</project>

```

`2`创建springmvc的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 开启注解扫描 -->
    <context:component-scan base-package="cn.zhanghuan"/>

    <!-- 视图解析器对象 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 开启SpringMVC框架注解的支持 -->
    <mvc:annotation-driven />

</beans>
```

`3`配置web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!--配置请求转发控制器，接收到的请求都转发给mvc-->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <!--配置解决中文乱码的过滤器-->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

</web-app>

```

`4`配置controller

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(){
        return "success";//将名为success.jsp文件返回
    }
}

```

---

**组成结构**

| 结构名            | 中文       | 解释                                             |
| ----------------- | ---------- | ------------------------------------------------ |
| DispatcherServlet | 前端控制器 | 接收并转发请求、响应                             |
| HandlerMapping    | 处理映射器 | 根据请求的url找到对应的处理函数                  |
| Handler           | 处理器     | 处理请求                                         |
| HandlerAdapter    | 处理适配器 | 把处理映射器转发过来的请求进行适配，再发给处理器 |
| ViewResolver      | 视图解析器 | 解析html文件                                     |

---

**请求**

`1`RequestMapping注解

将请求路径与处理方法建立映射关联，可放在方法上和类上，放在类上表示一级url，放在方法上表示二级url

可设置的属性

| 属性    | 功能                             | 值       |
| ------- | -------------------------------- | -------- |
| value   | 指定该类或方法对应的url          | 字符串   |
| method  | 指定该处理类或方法处理的请求方式 | {枚举类} |
| params  | 限制请求参数                     | {字符串} |
| headers | 限制请求头                       | {字符串} |

`2`请求参数绑定

1）处理方法中参数是字符串

请求中包含的参数名与处理方法中的参数名对应，请求中的参数会自动传入方法中

2）处理方法中参数是实体对象

请求中包含的参数名与处理方法中的参数实体对象的属性名对应，spring会先创建一个实体对象，再将参数赋值给实体对象的属性，并将实体对象传入方法中

(1)一般情况

```java
package cn.zhanghuan.controller;

import cn.zhanghuan.entity.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(User user){//user只有一个属性name，,访问链接为http://localhost:8080/springmvc_day01_01_start/aa?name=zhanghuan
        System.out.println (user);//User{name='zhanghuan'}
        return "success";
    }
}

```

(2)实体对象中包含其他实体的引用类型数据

```java
package cn.zhanghuan.controller;

import cn.zhanghuan.entity.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(User user){//user有两个属性，一个是name，另一个是department,访问链接为http://localhost:8080/springmvc_day01_01_start/aa?name=zhanghuan&department.name=tech
        System.out.println (user);//User{name='zhanghuan', department=Department{name='tech'}}
        return "success";
    }
}

```

(3)实体对象中包含list或map集合的引用类型数据

控制器类似，url应写为`http://localhost:8080/springmvc_day01_01_start/aa?name=zh&departments['0'].name=chanping`

`3`POST请求乱码问题处理

web.xml中配置springmvc的过滤器

```xml
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

`4`类型转换器

前台传到后台的参数，springmvc会自动封装，但有些参数如日期1991-2-11，无法进行封装，需使用类型转换器

1）自定义一个类实现Converter接口

```java
package cn.zhanghuan.util;


import org.springframework.core.convert.converter.Converter;

import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class string_to_dateConverter implements Converter<String, Date> {
    @Override
    public Date convert(String source) {
        if(source==null){
            throw new  RuntimeException("日期不可为空");
        }
        DateFormat df=new SimpleDateFormat ("yyyy-MM-dd");
        try {
            return  df.parse (source);
        } catch (ParseException e) {
            throw new  RuntimeException("日期类型转换错误");
        }
    }
}

```

2）配置springmvc.xml

```xml
<!--创建转换服务工厂的实例-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <bean class="cn.zhanghuan.util.string_to_dateConverter"></bean>
        </property>
    </bean>

    <!--将转换服务工厂的实例注入springmvc中-->
    <mvc:annotation-driven conversion-service="conversionService"/>
```

`5`在处理方法中获取原生ServletApi

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.ServletRequest;

@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say( ServletRequest request){
        System.out.println (request);
        return "success";
    }
}
```

`6`前后台参数名不一致问题解决RequestParam注解

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

//http://localhost:8080/springmvc_day01_01_start/aa?username=zh
//前台username和后台name不一致

@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say( @RequestParam(name="username") String name){
        System.out.println (name);
        return "success";
    }
}
```

`7`获取请求体内容RequestBody注解

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;


@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say( @RequestBody String json){
        System.out.println (json);
        return "success";
    }
}
```

> 注意
>
> 要将json数据转换成对象并封装到实体对象中，需要先导入jackson的3个依赖jar包

```
@ResponseBody
//这个可放置在类处理方法上，表示不用去找jsp文件，直接将字符串数据放到body里返回
```

`8`获取请求路径中的参数PathVariable注解

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;



@Controller
public class helloController {
    @RequestMapping(value="aa/{id}")//定义包含参数的路径
    public String say( @PathVariable int id){//获取路径中的参数
        System.out.println (id);
        return "success";
    }
}
```

`9`获取请求头RequestHeader注解

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RequestMapping;



@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(@RequestHeader("Accept") String accept){
        System.out.println (accept);
        return "success";
    }
}
```

`10`获取cookie的CookieValue注解

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.CookieValue;
import org.springframework.web.bind.annotation.RequestMapping;



@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(@CookieValue("JSESSIONID") String sessioned){
        System.out.println (sessioned);
        return "success";
    }
}
```

`11`请求中存储临时数据

1）ModelMap

数据会存到request域对象中

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.SessionAttributes;



@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(ModelMap model){
        model.addAttribute ("name","张欢");
        String name= (String) model.get("name");
        System.out.println (name);
        return "success";
    }
}
```

2）SessionAttributes注解

该注解只能在类上注解，将ModelMap对象中的存的键值对复制一份放到session域对象中

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.SessionAttributes;



@Controller
@SessionAttributes("name")
public class helloController {
    @RequestMapping(value="aa")//将ModelMap中的name键值对复制放入session域对象中
    public String say(ModelMap model){
        model.addAttribute ("name","张欢");
        return "success";
    }
}
```

删除session域中的所有键值对

```java
//部分代码
	@RequestMapping(value="bb")
    public  String delsession(SessionStatus status){
        status.setComplete ();
        return "success";
    }
```

`12`ModelAttribute注解

该注解修饰的方法，在其他方法执行前会先执行，其返回值会交给其他执行的方法

```java
package cn.zhanghuan.controller;

import cn.zhanghuan.entity.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class hiController {
    @RequestMapping(value="hi")
    public String say(User user) {
        System.out.println (user);//User{name='zhanghuan', date=null}
        return "success";
    }

    @ModelAttribute
    public User decorate_user(){
        User user=new User ();
        user.setName ("zhanghuan");
        return user;
    }
}

```

---

**响应**

`1`请求转发

(1)传统方式

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.io.IOException;


@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(){

        System.out.println ("this is aa");
        return "success";
    }

    @RequestMapping(value="bb")
    public  void dispacher(ServletRequest request, ServletResponse response){
        try {
            System.out.println ("this is bb");
            request.getRequestDispatcher ("/aa").forward (request,response);
        } catch (ServletException e) {
            e.printStackTrace ( );
        } catch (IOException e) {
            e.printStackTrace ( );
        }
        return ;
    }
}

```

(2)关键字方式

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.io.IOException;


@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(){

        System.out.println ("this is aa");
        return "success";
    }

    @RequestMapping(value="bb")
    public  String resp(ServletRequest request, ServletResponse response){
        return "forward:/aa";
    }
}

```

`2`重定向

(1)传统方式

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.ServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(){

        System.out.println ("this is aa");
        return "success";
    }

    @RequestMapping(value="bb")
    public  void resp(ServletRequest request, HttpServletResponse response){
        try {
            response.sendRedirect ("aa");
        } catch (IOException e) {
            e.printStackTrace ( );
        }
        return;
    }
}

```

(2)关键字方式

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class helloController {
    @RequestMapping(value="aa")
    public String say(){

        System.out.println ("this is aa");
        return "success";
    }

    @RequestMapping(value="bb")
    public  String resp(){
        return "redirect:/aa";
    }
}

```

`3`直接响应

(1)传统方式

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.io.IOException;


@Controller
public class helloController {

    @RequestMapping(value="bb")
    public  void resp(ServletRequest request, ServletResponse response){
        response.setCharacterEncoding ("utf-8");
        response.setContentType ("text/html");
        try {
            response.getWriter ().print ("hehe");
        } catch (IOException e) {
            e.printStackTrace ( );
        }
        return;
    }
}

```

(2)ModelAndView方式

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;


@Controller
public class helloController {

    @RequestMapping(value="bb")
    public  ModelAndView resp(){
        ModelAndView mv=new ModelAndView ();
        mv.addObject ("name","zhang");//域对象中设置属性
        mv.setViewName ("success");
        return mv;
    }
}

```

`4`静态资源拦截解除

springmvc.xml中新增

```xml
<!-- 设置静态资源不过滤 -->
    <mvc:resources location="/WEB-INF/js/" mapping="/js/**" />
```

`5`上传文件

前端要求:

form表单的enctype=multipart/form-data

method为post

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="uploadfile" enctype="multipart/form-data" method="post">
        <input type="file" value="请选择文件" name="f">
        <input type="submit" value="提交">
    </form>
</body>
</html>
```

依赖：

```xml
<dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.1</version>
    </dependency>
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.4</version>
    </dependency>
```

1）普通方式

```java
package cn.zhanghuan.controller;

import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.util.List;

@Controller
public class hiController {
    @RequestMapping(value="hi")
    public String say() {
        return "success";
    }
    @RequestMapping("uploadfile")
    @ResponseBody
    public String upload(HttpServletRequest request) throws Exception {
        DiskFileItemFactory factory=new DiskFileItemFactory ();
        ServletFileUpload upload=new ServletFileUpload (factory);
        List<FileItem> list=upload.parseRequest (request);
        String path=request.getSession ().getServletContext ().getRealPath ("/upload/");
        System.out.println (list);
        for (FileItem item:list
             ) {
            if(item.isFormField ()){
                System.out.println (item);
            }else{
                String filename=item.getName ();
                item.write (new File (path,filename));//写入
                item.delete ();
            }
        }
        System.out.println ("upload");
        return "ok";
    }

}

```

2）springmvc方式

(1)配置springmvc.xml文件

```
<!--配置上传文件解析插件，id必须是这个-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
```

(2)控制器

```java
package cn.zhanghuan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;


@Controller
public class hiController {
    @RequestMapping(value="hi")
    public String say() {
        return "success";
    }
    @RequestMapping("uploadfile")
    @ResponseBody
    public String upload(HttpServletRequest request,MultipartFile file) throws IOException {
        String name=file.getOriginalFilename ();
        String path=request.getSession ().getServletContext ().getRealPath ("/upload/");
        file.transferTo (new File (path,name));

        System.out.println ("upload");
        return "ok";
    }

}

```

3）跨服务器上传

(1)导入依赖

```xml
  <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-core</artifactId>
      <version>1.18.1</version>
    </dependency>
    <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-client</artifactId>
      <version>1.18.1</version>
    </dependency>
```

(2)代码

```java
//部分代码
@RequestMapping(value="bb")
    public  String resp(MultipartFile file) throws IOException {
        String name=file.getOriginalFilename ();
        String path="http://localhost:9000/springmvc_day01_01_start/uploadfile/";
        Client client=Client.create ();
        WebResource wbs=client.resource (path+name);
        wbs.put(file.getBytes ());
        return "success";
    }
```

`6`异常处理

项目中会出现各种异常，会层层抛出，最终抛出到用户哪里，影响体验。我们需要将异常捕获到，给用户返回一个友好的页面

(1)自定义异常

```java
package cn.zhanghuan.exception;

public class SyException extends Exception {
    private String message;

    public SyException(String message) {
        this.message = message;
    }

    public SyException(String message, String message1) {
        super (message);
        this.message = message1;
    }

    public SyException(String message, Throwable cause, String message1) {
        super (message, cause);
        this.message = message1;
    }

    public SyException(Throwable cause, String message) {
        super (cause);
        this.message = message;
    }

    public SyException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace, String message1) {
        super (message, cause, enableSuppression, writableStackTrace);
        this.message = message1;
    }

    @Override
    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}

```

(2)定义一个友好的异常页面

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false"%>
<html>
<head>
    <title>Title</title>
</head>
<body>
${error}
</body>
</html>
```

(3)定义异常处理类实现HandlerExceptionResolver接口

```java
package cn.zhanghuan.exception;

import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class SyExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        SyException e=null;
        if(ex instanceof SyException){
            e=(SyException)ex;
        }else{
            e=new SyException ("系統正在維護");
        }
        ModelAndView mv=new ModelAndView ();
        mv.addObject ("error",e.getMessage ());
        mv.setViewName ("error");
        return mv;
    }
}
```

(4)将异常处理类配置到spingmvc.xml

```xml
<!--将异常处理类实例化到容器中-->
<bean id="syExceptionResolver" class="cn.zhanghuan.exception.SyExceptionResolver"/>
```

`7`拦截器

1）过滤器和拦截器

| 过滤器                                     | 拦截器                                                 |
| ------------------------------------------ | ------------------------------------------------------ |
| Servlet规范中的一部分，所有javaweb工程可用 | springmvc工程可用                                      |
| url-pattern中配置/*后，对所有访问资源拦截  | 只拦截访问的控制器方法，如果访问的是jsp,html等不会拦截 |

2）使用

(1)定义一个类实现HandlerInterceptor接口

| 方法            | 调用时间                            | 执行顺序                     | 其他                                            |
| --------------- | ----------------------------------- | ---------------------------- | ----------------------------------------------- |
| preHandle       | Controller方法处理之前              | 按照声明的顺序一个接一个执行 | 若返回false，则中断执行                         |
| postHandle      | Controller方法处理完之后            | 按照声明的顺序倒着执行       | postHandle虽然post打头，但post、get方法都能处理 |
| afterCompletion | DispatcherServlet进行视图的渲染之后 |                              | 多用于清理资源                                  |

```java
package cn.zhanghuan.HandlerInterceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class allInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println ("请求来了，拦截器执行");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println ("响应来了，拦截器执行");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println ("渲染完成，清除资源");
    }
}

```

(2)在springmvc中配置

```xml
    <!--将拦截器对象注入容器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/*"></mvc:mapping>
            <bean class="cn.zhanghuan.HandlerInterceptor.allInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

---

**ssm整合**

实际开发中需要将spring,springmvc,mybatis进行整合，共同完成业务

1）创建web项目并导入依赖

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.2.RELEASE</spring.version>
    <slf4j.version>1.6.6</slf4j.version>
    <log4j.version>1.2.12</log4j.version>
    <mysql.version>5.1.6</mysql.version>
    <mybatis.version>3.4.5</mybatis.version>
  </properties>

  <dependencies>
    <!-- spring -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.6.8</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>compile</scope>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>${mysql.version}</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>jstl</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>

    <!-- log start -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>${log4j.version}</version>
    </dependency>

    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>${slf4j.version}</version>
    </dependency>

    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>${slf4j.version}</version>
    </dependency>

    <!-- log end -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>${mybatis.version}</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>

    <dependency>
      <groupId>c3p0</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.1.2</version>
      <type>jar</type>
      <scope>compile</scope>
    </dependency>
  </dependencies>
```

2）创建目录

```
-main
	-java
		-cn
			-zhang
				-controller
					helloController.java
				-dao
				-entity
					Account.java
				-service
					helloService.java
	-resources
		beans.xml
		log4j.properties
		springmvc.xml
	-webapp
		-WEB-INF
			-pages
				success.jsp
			web.xml
		index.jsp
```

3）创建配置文件

(1)web.xml

```xaml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>

    <!--通过监听器创建spring容器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:beans.xml</param-value>
    </context-param>

    <!--前端控制器-->
  <display-name>Archetype Created Web Application</display-name>
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

    <!--字符编码过滤器-->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

</web-app>


```

(2)springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="cn.zhang">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller" />
    </context:component-scan>

    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

    <mvc:annotation-driven />
</beans>
```

(3)beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">

    <context:component-scan base-package="cn.zhang">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"></context:exclude-filter>
    </context:component-scan>

    <!--mybatis配置-->
    <!--创建数据源对象-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test2?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="user" value="root"/>
        <property name="password" value=""/>
    </bean>
    <!--创建sqlSessionFactory对象-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--创建扫描有sql语句的接口并创建其实现类对象-->
    <bean id="mapperScan" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="cn.zhang.dao" />
    </bean>

    <!--配置事务通知并通过aop增强-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <tx:advice id="transactionInterceptor" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="*find*" read-only="true" />
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>
    <aop:config>
        <aop:pointcut id="p1" expression="execution(* cn.zhang.service.*.*(..))"></aop:pointcut>
        <aop:advisor advice-ref="transactionInterceptor" pointcut-ref="p1"></aop:advisor>
    </aop:config>

</beans>
```


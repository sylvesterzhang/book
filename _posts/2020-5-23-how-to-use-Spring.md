---
layout: post
title: Spring
tags: java
categories: backends
---

简介、耦合、控制反转、依赖注入、注解方式反转控制和依赖注入、Spring整合Junit、银行转账案例、代理、AOP面向切面编程、JDBCTemplate

**简介**

`1`核心内容

IOC反向控制、AOP面向切面编程

`2`优势

方便解耦，简化编程

AOP编程支持

声明式事务支持

方便程序的测试

方便集成各种优秀的框架

降低JAVAEE API的使用难度

JAVA源码是经典的学习范例

`3`四大核心容器

Beans、Core、Context、SpEL

---

**耦合**

程序间的依赖关系称为耦合。解耦就是降低程序间的依赖关系。

`1`解耦案例一

```java
DriverManager,registeDriver(new com.mysql.jdbc.Driver());
//编译依赖

class.forName("com.mysql.jdbc.Driver")
//运行依赖
    
//分析：如果删除掉com.mysql.jdbc.Driver文件，前者在编译时报错，后者在运行时报错
```

避免使用new关键字，通过读取配置文件来获取要创建对象的全类名，再使用反射来创建对象

`2`Bean的概念

Bean表示可重复使用的组件

JavaBean表示java语言编写的重复使用的组件

`3`工厂模式案例

1）Bean.properties

```
service1=services.service1
```

2）BeanFactory.java

```java
package client;

import java.io.IOException;
import java.io.InputStream;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.Properties;

public class BeanFactory {
    static private Properties properties;
    static private HashMap<String,Object> map;
    static {
        try {
            //读取配置文件
            InputStream in=BeanFactory.class.getClassLoader ().getResourceAsStream ("Bean.properties");
            properties=new Properties ();
            properties.load (in);

            //创建实例并放入容器中
            Enumeration keys=properties.keys ();
            map=new HashMap<String, Object> ();
            while (keys.hasMoreElements ()){
                String key=keys.nextElement ().toString ();
                Object obj=Class.forName (properties.getProperty (key)).newInstance ();
                map.put (key,obj);
            }
        } catch (IOException e) {
            e.printStackTrace ( );
        } catch (IllegalAccessException e) {
            e.printStackTrace ( );
        } catch (InstantiationException e) {
            e.printStackTrace ( );
        } catch (ClassNotFoundException e) {
            e.printStackTrace ( );
        }
    }

    public static Object getBean(String beanname){
        return map.get (beanname);
    }
}

```

3）clientMain

```java
package client;

import services.service1;

public class clientMain {

    public static void main(String[] args) {
        for (int i = 0; i <5 ; i++) {
            service1 s=(service1) BeanFactory.getBean ("service1");
            System.out.println (s);
            s.service ();
        }
    }
}
//执行的是同一个实例的方法
```

---

**控制反转**

把创建对象的权力交给框架，此过程称为控制反转，是框架的重要特性，包括依赖注入、依赖查找

`1`Spring IOC的基本使用

1）pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.zhanghuan</groupId>
    <artifactId>sprignioc</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency><!--导入相关依赖-->
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
    </dependencies>

</project>
```

2）beans配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="service1" class="services.service1"></bean>
</beans>
```

3）client.java

```java
package client;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import services.service1;

public class clientMain {

    public static void main(String[] args) {
        ApplicationContext ac=new ClassPathXmlApplicationContext ("beans.xml");
        //创建Bean对象方式一
        service1 s=(service1)ac.getBean ("service1");

        //创建Bean对象方式二
        service1 s1=ac.getBean ("service1",service1.class);

        //总之，通过xml文件配置的id获取bean
        
        s.service ();
        s1.service ();
    }
}
```

`2`ApplicationContext接口的3种实现类

| 实现类                             | 传入配置文件特点                 |
| ---------------------------------- | -------------------------------- |
| ClassPathXmlApplicationContext     | 配置文件在类路径下               |
| FileSystemXmlApplicationContext    | 配置文件在磁盘任意未知           |
| AnnotationConfigApplicationContext | 无配置文件，配置信息通过注解设置 |

`3`核心容器的接口

| 接口名             | 特点                         |
| ------------------ | ---------------------------- |
| ApplicationContext | 读取完配置文件，立即创建对象 |
| BeanFactory        | 读取完配置文件，延迟创建对象 |

常用的是ApplicationContext，可自动根据不同情况实现单例或多例模式

`4`创建Bean的三种方式

1）默认构造函数创建

```xml
<!--省略部分代码-->
<bean id="service1" class="services.service1"></bean>
```

如果对象无默认构造函数，则无法创建实例

2）使用工厂普通方法创建

```java
package services;
//这是工厂类
public class serviceFactory {
    service1 build_service(){
        return new service1 ();
    }
}
```

```xml
<!--省略部分代码-->
<bean id="serviceFactory" class="services.serviceFactory"></bean>
<bean id="service_from_factory1" factory-bean="serviceFactory" factory-method="build_service"></bean>
```

3）使用工厂静态方法创建

```java
package services;
//这是工厂类
public class serviceStaticFactory {
    static service1 build_service(){
        return new service1 ();
    }
}
```

```xml
<!--省略部分代码-->
<bean id="serviceStaticFactory" class="services.serviceStaticFactory" factory-method="build_service"></bean>
```

`5`bean标签的scope属性

| 属性名         | 说明                     |
| -------------- | ------------------------ |
| singleton      | 单例                     |
| prototype      | 多例                     |
| request        | 作用于web应用的请求范围  |
| session        | 作用于会话的请求范围     |
| global-session | 作用于集群会话的请求范围 |

---

**依赖注入**

依赖关系的维护交给spring来做，此过程称为依赖注入。（创建对象的过程中，将参数传入，参数可能是其他对象，使得对象间产生依赖）

`1`可注入的类

基本类型和String类型

其他bean（配置文件中配置过的或注解配置过的bean）

复合类型/集合类型

> 注意
>
> 经常变化的不要注入

`2`注入方式

1）构造方法注入

```xml
<bean id="student" class="doMain.Student">
        <constructor-arg name="id" value="1"></constructor-arg>
        <constructor-arg name="male" value="男"></constructor-arg>
        <constructor-arg name="name" value="zhanghuan"></constructor-arg>
        <constructor-arg name="math" value="95"></constructor-arg>
        <constructor-arg name="english" value="100"></constructor-arg>
</bean>
```

需要注意的是，doMain.Student类里必须要有对应参数的构造方法，否则无法注入，其本质是调用构造方法注入

constructor-arg标签的属性

| 属性  | 解释                                       |
| ----- | ------------------------------------------ |
| type  | 要注入的参数的类型                         |
| index | 要注入的参数的下标                         |
| name  | 要注入的参数的参数名                       |
| value | 要注入的参数的值，支持字符串和数字         |
| ref   | 要注入的参数的值，支持配置文件中的其他bean |

2）setter方法注入

```xml
<bean id="student" class="doMain.Student">
        <property name="id" value="1"></property>
        <property name="male" value="男"></property>
        <property name="name" value="zhanghuan"></property>
        <property name="math" value="95"></property>
        <property name="english" value="100"></property>
</bean>
```

property 标签的属性

| 属性  | 解释                                       |
| ----- | ------------------------------------------ |
| name  | 要注入的参数的参数名                       |
| value | 要注入的参数的值，支持字符串和数字         |
| ref   | 要注入的参数的值，支持配置文件中的其他bean |

复杂类型通过setter方法注入

```java
package doMain;

import java.util.List;
import java.util.Map;

//目标bean
public class LoveYou {
    private List<Student> students;
    private Map<String,String> map;

    public List<Student> getStudents() {
        return students;
    }

    public void setStudents(List<Student> students) {
        this.students = students;
    }

    public Map<String, String> getMap() {
        return map;
    }

    public void setMap(Map<String, String> map) {
        this.map = map;
    }

    @Override
    public String toString() {
        return "LoveYou{" +
                "students=" + students +
                ", map=" + map +
                '}';
    }
}

```

```xml
<bean id="loveyou" class="doMain.LoveYou">
        <property name="students">
            <list>
                <ref bean="student"/>
                <ref bean="student"/>
            </list>
        </property>
        <property name="map">
            <map>
                <entry key="name" value="zhanghuan"></entry>
            </map>
        </property>
</bean>
```

列表类型数据可选标签：list，array，set

键值对数据可选标签：map，props

---

**注解方式反转控制和依赖注入**

`1`xml方式和注解方式对比

| 功能           | xml配置文件        | 注解                                    |
| -------------- | ------------------ | --------------------------------------- |
| 创建对象       | bean标签           | Component,Controller,Service,Repository |
| 注入依赖(数据) | property标签       | Autowired,Quqlifier,Resource,Value      |
| 改变作用范围   | scope属性          | Scope                                   |
| 生命周期相关   | init-method属性    | Postconstruct                           |
|                | destroy-method属性 | PreDestroy                              |

`2`各注解功能

1）Component注解

用于把当前类对象存入spring容器中，属性value设置该bean的id，如果不设置默认为该类的小写名。Controller,Service,Repository和该注解的功能一样。

使用前需要在配置xml文件中进行如下配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="doMain"></context:component-scan>
    <!--扫描文件夹，将里的类实例化后放入容器-->
</beans>
```

2）注入bean

(1)Autowired注解

指定注入依赖，可配置在类的变量上，也可配置在类的方法上。在变量上配置该注解后，该变量的setter方法可省去。注入的前提是，容器中有唯一一个bean对象和要注入的变量类型匹配，否则导致注入失败而报错。

(2)Qualifier注解

不可以单独使用，必须和Autowored合用，value属性值设置注入bean的id

(3)Resource注解

可单独使用，name属性值设置注入bean的id

> 注意
>
> 以上3中方式只可注入bean类型，无法注入基本类型和String类型，集合类型只能通过xml方式注入

3）注入基本类型和String类型

Value注解

其value属性值为要注入的基本类型和String类型数据，可以使用spring中的SpEl表达式

4）Scope注解

其value属性值为singleton和prototype，控制类的使用方式是单例还是多例

5）生命周期相关注解

  Postconstruct注解和PreDestroy注解，主要在单例模式下使用，value值为指定的类的构造方法和析构方法

`3`数据库增删改查案例

1）pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.zhanghuan</groupId>
    <artifactId>annotationspring</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>


        <dependency>
            <groupId>commons-dbutils</groupId>
            <artifactId>commons-dbutils</artifactId>
            <version>1.4</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>

        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.10</version>
        </dependency>
    </dependencies>

</project>
```

2）StudentDaoImpl.java

```java
package com.rootpackage.dao;

import com.rootpackage.doMain.Student;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;


import java.sql.SQLException;
import java.util.List;

@Repository("studentdao")
public class StudentDaoImpl implements StudentDao {
    @Autowired
    private QueryRunner runner;

    public void setRunner(QueryRunner runner) {
        this.runner = runner;
    }

    public void insertOne(Student student) {
        try {
            runner.update ("insert into student (male,name,math,english) values(?,?,?,?)",student.getMale (),student.getName (),student.getMath (),student.getEnglish ());
        } catch (SQLException e) {
            e.printStackTrace ( );
        }
    }

    public void deleteOne(int id) {
        try {
            runner.update ("delete from student where id=?",id);
        } catch (SQLException e) {
            e.printStackTrace ( );
        }
    }

    public void updateOne(Student student) {
        try {
            runner.update ("update student set male=?,name=?,math=?,english=? where id=?",student.getMale (),student.getName (),student.getMath (),student.getEnglish (),student.getId ());
        } catch (SQLException e) {
            e.printStackTrace ( );
        }
    }

    public Student findOne(int id) {
        try {
            return runner.query ("select * from student where id=?",new BeanHandler<Student> (Student.class),id);
        } catch (SQLException e) {
            throw new RuntimeException ();
        }
    }

    public List<Student> findAll() {
        try {
            return runner.query ("select * from student",new BeanListHandler<Student> (Student.class));
        } catch (SQLException e) {
            throw new RuntimeException ();
        }
    }
}

```



3）StudentServiceImpl.java

```java
package com.rootpackage.services;

import com.rootpackage.dao.StudentDao;
import com.rootpackage.doMain.Student;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository("studentserviceimpl")
public class StudentServiceImpl implements StudentService {
    @Autowired
    private StudentDao dao;

    public void insertOne(Student student) {
        dao.insertOne (student);
    }

    public void deleteOne(int id) {
        dao.deleteOne (id);
    }

    public void updateOne(Student student) {
        dao.updateOne (student);
    }

    public Student findOne(int id) {

        return dao.findOne (id);
    }

    public List<Student> findAll() {

        return dao.findAll ();
    }
}

```



4）beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.rootpackage"></context:component-scan>
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <constructor-arg name="ds" ref="datasource"></constructor-arg>
    </bean>
    <bean id="datasource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/test1?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8"></property>
        <property name="user" value="root"></property>
        <property name="password" value=""></property>
    </bean>
</beans>
```



5）test01.java

```java

import com.rootpackage.doMain.Student;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.rootpackage.services.StudentServiceImpl;

import java.util.List;


public class test01 {
    private StudentServiceImpl s=null;
    @Before
    public void init(){
        ApplicationContext ac=new ClassPathXmlApplicationContext ("beans.xml");
        s=ac.getBean ("studentserviceimpl",StudentServiceImpl.class);
    }

    @Test
    public void findal(){
        List<Student> list=s.findAll ();
        for (Student i:
                list) {
            System.out.println (i);
        }
    }

    @Test
    public void insertone(){
        Student student=new Student ();
        student.setMale ("男");
        student.setName ("sylvester");
        student.setMath (85);
        student.setEnglish (90);
        s.insertOne (student);
    }

    @Test
    public void deleteone(){
        s.deleteOne (13);
    }

    @Test
    public void updateone(){
        Student student=new Student ();
        student.setId (15);
        student.setMale ("男");
        student.setName ("sylvester1");
        student.setMath (85);
        student.setEnglish (90);
        s.updateOne (student);
    }

    @Test
    public void findone(){
        Student student=s.findOne (15);
        System.out.println (student);
    }
}

```

`4`配置类

1）Configuration注解

该注解标志的类表示是一个配置类

2）ComponentScan注解

指定创建容器扫描的包，value值为要扫描的包的位置

3）Bean注解

修饰方法，该方法的返回值将会被存入spring的ioc容器中

```java
//注解配置方式创建容器
ApplicationContext ac=new AnnotationConfigApplicationContext (springConfig.class,jdbcConfig.class);//这里可传入多个字节码文件，传入的字节码文件对应的类的Configuration注解可省略
s=ac.getBean ("studentserviceimpl",StudentServiceImpl.class);
```

```java
package config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;


@Configuration
@ComponentScan("com.rootpackage")
public class springConfig {

}
```

```java
package config;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.beans.PropertyVetoException;

@Configuration
public class jdbcConfig {
    @Bean
    public DataSource createDataSource(){
        try {
            ComboPooledDataSource ds=new ComboPooledDataSource();
            ds.setDriverClass ("com.mysql.jdbc.Driver");
            ds.setJdbcUrl ("jdbc:mysql://127.0.0.1:3306/test1?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8");
            ds.setUser ("root");
            ds.setPassword ("");
            return ds;
        } catch (PropertyVetoException e) {
            throw new RuntimeException (e);
        }
    }

    @Bean
    public QueryRunner createQueryRunner(DataSource ds){
        return new QueryRunner (ds);
    }
}
```

4）Import注解

在一个配置类中，导入另一个配置类，一般是父配置类v导入子配置类。value值为目标配置类的字节码文件

```java
package config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;


@Configuration
@ComponentScan("com.rootpackage")
@Import (jdbcConfig.class)//这里将子配置文件导入
public class springConfig {

}
```

```java
ApplicationContext ac=new AnnotationConfigApplicationContext (springConfig.class);//创建容器时只需将父配置文件的字节码文件导入即可
```

5）Scope注解

value值标记实例是单例的还是多例的

6）PropertySource注解

将xxx.properties文件读取到spring的环境变量中，在变量上用@Value("${变量名}")可取到变量值

```
#duid.properties
# 注意:用户这里的变量是user，不可用username，username默认是admin无法使用
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/test1?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
user=root
password=
```

```java
package config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;


@Configuration
@ComponentScan("com.rootpackage")
@Import (jdbcConfig.class)
@PropertySource("classpath:druid.properties")//将properties文件读取到spring的环境中
public class springConfig {

}
```

```java
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.beans.PropertyVetoException;

@Configuration
public class jdbcConfig {
    @Value ("${driverClassName}")//spEl表达式将变量读取到这里，并注入给变量
    private  String driverClassName;

    @Value ("${url}")
    private  String url;

    @Value ("${user}")
    private  String user;

    @Value ("${password}")
    private  String password;
    @Bean
    public DataSource createDataSource(){
        try {
            ComboPooledDataSource ds=new ComboPooledDataSource();
            ds.setDriverClass (driverClassName);
            ds.setJdbcUrl (url);
            ds.setUser (user);
            ds.setPassword (password);
            return ds;
        } catch (PropertyVetoException e) {
            throw new RuntimeException (e);
        }
    }

    @Bean
    public QueryRunner createQueryRunner(DataSource ds){
        return new QueryRunner (ds);
    }
}
```

7）Qualifier注解的另一种用法

```java
package config;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.beans.PropertyVetoException;

@Configuration
public class jdbcConfig {
    @Value ("${driverClassName}")
    private  String driverClassName;

    @Value ("${url}")
    private  String url;

    @Value ("${user}")
    private  String user;

    @Value ("${password}")
    private  String password;
    
    @Bean("ds1")
    public DataSource createDataSource(){
        try {
            ComboPooledDataSource ds=new ComboPooledDataSource();
            ds.setDriverClass (driverClassName);
            ds.setJdbcUrl (url);
            ds.setUser (user);
            ds.setPassword (password);
            return ds;
        } catch (PropertyVetoException e) {
            throw new RuntimeException (e);
        }
    }

    @Bean("ds2")
    public DataSource createDataSource1(){
        try {
            ComboPooledDataSource ds=new ComboPooledDataSource();
            ds.setDriverClass (driverClassName);
            ds.setJdbcUrl (url);
            ds.setUser (user);
            ds.setPassword (password);
            return ds;
        } catch (PropertyVetoException e) {
            throw new RuntimeException (e);
        }
    }

    //这里有两个datasource分别是ds1和ds2，默认情况下会无法注入而报错
    @Bean
    public QueryRunner createQueryRunner(@Qualifier("ds1")  DataSource ds){
        //此处的@Qualifier指定将ds1注入，就不会报错了
        return new QueryRunner (ds);
    }
}

```

---

**Spring整合Junit**

默认情况，测试时需要创建容器，获取容器中要测试的对象。为了方便，我们想在测试时，也想让程序自动创建容器且自动注入对象，需要使用Spring的整合Junit的依赖

1）pom.xml中导入依赖

```
		<dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
```

注意两个依赖的版本，这里版本不对会报错

2）测试类上使用注解创建容器和自动注入

```java

import com.rootpackage.doMain.Student;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import com.rootpackage.services.StudentServiceImpl;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;;

import java.util.List;

@RunWith (SpringJUnit4ClassRunner.class)//这个注解表示这个测试类是整合spring的
//@ContextConfiguration(classes=springConfig.class)
@ContextConfiguration(locations = "classpath:beans.xml")//创建容器，将配置文件导入，支持两种配置文件
public class test01 {
    @Autowired
    private StudentServiceImpl s=null;


    @Test
    public void findal(){
        List<Student> list=s.findAll ();
        for (Student i:
                list) {
            System.out.println (i);
        }
    }

    @Test
    public void insertone(){
        Student student=new Student ();
        student.setMale ("男");
        student.setName ("sylvester");
        student.setMath (85);
        student.setEnglish (90);
        s.insertOne (student);
    }

    @Test
    public void deleteone(){
        s.deleteOne (13);
    }

    @Test
    public void updateone(){
        Student student=new Student ();
        student.setId (15);
        student.setMale ("男");
        student.setName ("sylvester1");
        student.setMath (85);
        student.setEnglish (90);
        s.updateOne (student);
    }

    @Test
    public void findone(){
        Student student=s.findOne (15);
        System.out.println (student);
    }
}

```

---

**银行转账案例**

分析：转账过程中涉及事务操作，业务层包含持久层的多个操作，而者多个操作默认情况是多个事务，转账过程执行中报错易导致总数据量发生变化。为解决这个问题，我们使用Threadlocal将数据库的Connection存放进去，在业务层运行时，直接通过业务层的Threadlocal获取出Connection，开启事务进行一些列操作。

`1`目录结构

```
-main
	-java
		-com
            -rootpackage
                -dao
                    --accountDao
                    --accountDaoImpl
                -doMain
                    --Account
                -service
                    --AccountService
                    --AccountServiceImpl
                -utils
                    --ConnectionUtils
                    --TransactionManager
		-config
			--jdbcConfig
			--SpringConfig
	-resources
		--druid.properties
-test
	-java
		--test1
```

`2`文件代码

```java
package com.rootpackage.dao;

import com.rootpackage.doMain.Account;

import java.util.List;

public interface accountDao {
    List<Account> findAll();
    void addOme(Account account);
    void deleteOne(int id);
    void updateOne(Account account);
    Account findOne(int id);
}

```

```java
package com.rootpackage.dao;

import com.rootpackage.doMain.Account;
import com.rootpackage.utils.ConnectionUtils;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.sql.SQLException;
import java.util.List;
@Component
public class accountDaoImpl implements accountDao {
    @Autowired
    private QueryRunner runner;
    @Autowired
    private ConnectionUtils connectionUtils;
    public List<Account> findAll() {
        try {
            return runner.query (connectionUtils.getThreadConnection (),"select * from account",new BeanListHandler<Account> (Account.class));
        } catch (SQLException e) {
            throw new RuntimeException (e);
        }
    }

    public void addOme(Account account) {
        try {
            runner.update (connectionUtils.getThreadConnection (),"insert into account (name,money) values (?,?)",account.getName (),account.getMoney ());
        } catch (SQLException e) {
            throw new RuntimeException (e);
        }
    }

    public void deleteOne(int id) {
        try {
            runner.update (connectionUtils.getThreadConnection (),"delete from account where id=?",id);
        } catch (SQLException e) {
            throw new RuntimeException (e);
        }
    }

    public void updateOne(Account account) {
        try {
            runner.update (connectionUtils.getThreadConnection (),"update account set name=?,money=? where id=?",account.getName (),account.getMoney (),account.getId ());
        } catch (SQLException e) {
            throw new RuntimeException (e);
        }
    }

    public Account findOne(int id) {
        try {
            return runner.query (connectionUtils.getThreadConnection (),"select * from account where id=?",new BeanHandler<Account> (Account.class),id);
        } catch (SQLException e) {
            throw new RuntimeException (e);
        }
    }
}

```

```java
package com.rootpackage.doMain;

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

```java
package com.rootpackage.service;

import com.rootpackage.doMain.Account;

import java.util.List;

public interface AccountService {
    List<Account> findAll();
    void addOme(Account account);
    void deleteOne(int id);
    void updateOne(Account account);
    Account findOne(int id);
    void transfer(int id1,int id2,int money);
}

```

```java
package com.rootpackage.service;

import com.rootpackage.doMain.Account;
import com.rootpackage.utils.TransactionManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.List;
import com.rootpackage.dao.accountDaoImpl;

@Component
public class AccountServiceImpl implements AccountService {
    @Autowired
    private accountDaoImpl dao;
    @Autowired
    private TransactionManager manager;

    public List<Account> findAll() {
        try {
            manager.beginTransaction ();
            List<Account> list=dao.findAll ();
            manager.commit ();
            return list;
        } catch (Exception e) {
            manager.rollback ();
            throw new RuntimeException (e);
        } finally {
            manager.release ();
        }
    }

    public void addOme(Account account) {
        try {
            manager.beginTransaction ();
            dao.addOme (account);
            manager.commit ();
        } catch (Exception e) {
            manager.rollback ();
            throw new RuntimeException (e);
        } finally {
            manager.release ();
        }
    }

    public void deleteOne(int id) {
        try {
            manager.beginTransaction ();
            dao.deleteOne (id);
            manager.commit ();
        } catch (Exception e) {
            manager.rollback ();
            throw new RuntimeException (e);
        } finally {
            manager.release ();
        }
    }

    public void updateOne(Account account) {
        try {
            manager.beginTransaction ();
            dao.updateOne (account);
            manager.commit ();
        } catch (Exception e) {
            manager.rollback ();
            throw new RuntimeException (e);
        } finally {
            manager.release ();
        }
    }

    public Account findOne(int id) {
        try {
            manager.beginTransaction ();
            Account a=dao.findOne (id);
            manager.commit ();
            return a;
        } catch (Exception e) {
            manager.rollback ();
            throw new RuntimeException (e);
        } finally {
            manager.release ();
        }
    }

    public void transfer(int id1, int id2, int money) {
        try {
            manager.beginTransaction ();
            Account a1=dao.findOne (id1);
            Account a2=dao.findOne (id2);
            a1.setMoney (a1.getMoney ()-money);
            a2.setMoney (a2.getMoney ()+money);
            dao.updateOne (a1);
            int a=10/0;//这里会报错，但是会进行回滚，数据总量不会发生改变
            dao.updateOne (a2);
            manager.commit ();
        } catch (Exception e) {
            manager.rollback ();
            throw new RuntimeException (e);
        } finally {
            manager.release ();
        }
    }
}

```

```java
package com.rootpackage.utils;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

@Component
public class ConnectionUtils {
    private ThreadLocal<Connection> t1=new ThreadLocal<Connection> ();
    //一个线程内部的存储类，可以在指定线程内存储数据，数据存储以后，只有指定线程可以得到存储数据
    @Autowired
    private DataSource dataSource;

    public Connection getThreadConnection(){
        try {
            Connection con=t1.get ();
            if(con==null){
                con=dataSource.getConnection ();
                t1.set (con);
            }
            return con;
        } catch (SQLException e) {
            throw new RuntimeException (e);
        }
    }
    public void removeConnection(){
        t1.remove ();
    }
}

```

```java
package com.rootpackage.utils;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.sql.SQLException;

@Component
public class TransactionManager {
    @Autowired
    private ConnectionUtils connectionUtils;

    public void beginTransaction(){
        try {
            connectionUtils.getThreadConnection ().setAutoCommit (false);
        } catch (SQLException e) {
            e.printStackTrace ( );
        }
    }
    public void commit(){
        try {
            connectionUtils.getThreadConnection ().commit ();
        } catch (SQLException e) {
            e.printStackTrace ( );
        }
    }
    public void rollback(){
        try {
            connectionUtils.getThreadConnection ().rollback ();
        } catch (SQLException e) {
            e.printStackTrace ( );
        }
    }
    public void release(){
        connectionUtils.removeConnection ();
    }
}

```

```jade
package config;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.beans.PropertyVetoException;

@Configuration
public class jdbcConfig {
    @Value ("${driverClassName}")
    private  String driverClassName;

    @Value ("${url}")
    private  String url;

    @Value ("${user}")
    private  String user;

    @Value ("${password}")
    private  String password;

    @Bean
    public DataSource createdatasource(){
        try {
            ComboPooledDataSource ds=new ComboPooledDataSource();
            ds.setDriverClass (driverClassName);
            ds.setJdbcUrl (url);
            ds.setUser (user);
            ds.setPassword (password);
            return ds;
        } catch (PropertyVetoException e) {
            throw new RuntimeException (e);
        }
    }

    @Bean
    public QueryRunner createqueryrunner(DataSource ds){
        return new QueryRunner (ds);
    }
}

```

```java
package config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

@Configuration
@ComponentScan("com.rootpackage")
@PropertySource("classpath:druid.properties")
@Import (jdbcConfig.class)
public class SpringConfig {

}

```

```
#duid.properties
# 注意:用户这里的变量是user，不可用username，username默认是admin无法使用
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/test2?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
user=root
password=
```

```java
import com.rootpackage.doMain.Account;
import com.rootpackage.service.AccountServiceImpl;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import config.SpringConfig;

import java.util.List;

@RunWith (SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class test1 {
    @Autowired
    private AccountServiceImpl service;
    @Test
    public void findall(){
        List<Account> list=service.findAll ();
        for (Account a:
             list) {
            System.out.println (a);
        }
    }
    @Test
    public void transfer(){

        service.transfer (1,2,500);
    }
}

```

---

**代理**

`1`jdk中的代理类

java在JDK1.5之后提供了一个"java.lang.reflect.Proxy"类，通过"Proxy"类提供的一个newProxyInstance方法用来创建一个对象的代理对象

```java
//部分代码
final computermaker m=new computermaker ();//该被代理对象必须实现一个接口,并且final关键字修饰
        saler n=(saler) Proxy.newProxyInstance (m.getClass ( ).getClassLoader ( ), m.getClass ().getInterfaces (), new InvocationHandler ( ) {
            /*
            ClassLoader 类加载器,让代理对象和被代理对象使用相同的类加载器
            Class[] 字节码数组，让代理对象和被代理对象有相同的方法
            InvocationHandler 需要创建一个该接口的匿名内部类，并重写invoke方法以增强
             */
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                return method.invoke (m,args);//注意：第一个参数是被代理对象，最后一个参数是该方法传过来的参数
            }
        });
        n.sale ();
```

`2`第三方代理类

由于jdk提供的代理要求被代理对象必须实现接口，对于没有实现接口的被代理对象可使用第三方代理类

1）pom.xml导入依赖

```xml
		<dependency>
            <groupId>cglib</groupId>
            <artifactId>cglib</artifactId>
            <version>3.3.0</version>
        </dependency>
```

2）部分代码

```java
final computermaker m=new computermaker ();
        computermaker n=(computermaker)Enhancer.create (m.getClass ( ), new MethodInterceptor ( ) {
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                return method.invoke (m,objects);
            }
        });
        n.sale ();
```

`3`转账案例改用代理

1）AccountServiceImpl修改简化

```java
package com.rootpackage.service;

import com.rootpackage.doMain.Account;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.List;
import com.rootpackage.dao.accountDaoImpl;

@Component("accountservice")
public class AccountServiceImpl implements AccountService {
    @Autowired
    private accountDaoImpl dao;


    public List<Account> findAll() {
        List<Account> list=dao.findAll ();
        return list;
    }

    public void addOme(Account account) {
        dao.addOme (account);
    }

    public void deleteOne(int id) {
        dao.deleteOne (id);
    }

    public void updateOne(Account account) {
        dao.updateOne (account);
    }

    public Account findOne(int id) {
        Account a=dao.findOne (id);
        return a;
    }

    public void transfer(int id1, int id2, int money) {
        Account a1=dao.findOne (id1);
        Account a2=dao.findOne (id2);
        a1.setMoney (a1.getMoney ()-money);
        a2.setMoney (a2.getMoney ()+money);
        dao.updateOne (a1);
        int a=9/0;
        dao.updateOne (a2);
    }
}

```

2）新建代理工厂

```java
package com.rootpackage.factory;

import com.rootpackage.service.AccountService;
import com.rootpackage.utils.TransactionManager;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;

@Component
public class BeanFactory {
    @Autowired
    @Qualifier("accountservice")
    private AccountService accountService;
    @Autowired
    private TransactionManager manager;
    public AccountService getAccountService(){
        return (AccountService)Enhancer.create (accountService.getClass ( ), new MethodInterceptor ( ) {
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                Object result=null;
                try {
                    manager.beginTransaction ();
                    result=method.invoke (accountService,objects);
                    manager.commit ();
                    return result;
                } catch (Exception e) {
                    manager.rollback ();
                    throw new RuntimeException (e);
                } finally {
                    manager.release ();
                }
            }
        });
    }
}

```

3）测试类

```java
import com.rootpackage.doMain.Account;
import com.rootpackage.factory.BeanFactory;
import com.rootpackage.service.AccountServiceImpl;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import config.SpringConfig;

import java.util.List;

@RunWith (SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class test1 {
    @Autowired
    private BeanFactory factory;
    @Test
    public void findall(){
        List<Account> list=factory.getAccountService ().findAll ();//通过代理工厂获取AccountService对象
        for (Account a:
             list) {
            System.out.println (a);
        }
    }
    @Test
    public void transfer(){

        factory.getAccountService ().transfer (1,2,500);
    }

}

```

---

**AOP面向切面编程**

Aspect Oriented Programming，是spring框架中一个重要内容，是函数式编程的一种衍生范型。利用AOP可对业务逻辑各个部分进行隔离，从而使业务逻辑各个部分间耦合度降低。

特点：在程序运行期间，不改变源码对已有方法进行增强

优势：减少重复代码，提高开发效率，维护方便

`1`常用名词

| 名词     | 英文         | 解释                                       |
| -------- | ------------ | ------------------------------------------ |
| 连接点   | joinpoint    | 已经被增强的挂载点，如增强的方法           |
| 切入点   | pointcut     | 所有可以被拦截的点（方法）                 |
| 通知     | advice       | 拦截到切入点后要做的事情                   |
| 映入     | introduction | 运行期动态添加的方法或Field                |
| 目标对象 | target       | 代理的目标对象                             |
| 织入     | weaving      | 增强应用到目标对象来创建新的代理对象的过程 |
| 代理     | proxy        | 一个类被aop增强后的一个结果类              |
| 切面     | aspect       | 切入点和通知的结合                         |

`2`快速开始

1）pom.xml导入依赖

```
		<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.7</version>
        </dependency>
```

2）定义一个服务类

```java
package com.rootpackage.services;

public class service1 {
    public void doservice(){
        System.out.println ("开始服务……");
    }
}

```

3）定义一个日志类

```java
package com.rootpackage.mout;

public class log {
    public void dolog1(){
        System.out.println ("打印日志1");
    }
    public void dolog2(){
        System.out.println ("打印日志2");
    }
    public void dolog3(){
        System.out.println ("打印日志3");
    }
    public void dolog4(){
        System.out.println ("打印日志4");
    }
}

```

4）beans.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="service1" class="com.rootpackage.services.service1"></bean>
    <bean id="log" class="com.rootpackage.mout.log"></bean>
    <aop:config>
        <aop:aspect id="asp1" ref="log">
            <!--id是给这个切面的id，ref指向通知类（放入切入点的方法所属的类）-->
            <aop:before method="dolog1" pointcut="execution(* *..*.doservice(..))"></aop:before>
            <!--method是通知方法的方法名，pointcut切入点-->
            <aop:after-returning method="dolog2" pointcut="execution(* *..*.doservice(..))"></aop:after-returning>
            <aop:after-throwing method="dolog3" pointcut="execution(* *..*.doservice(..))"></aop:after-throwing>
            <aop:after  method="dolog4" pointcut="execution(* *..*.doservice(..))"></aop:after>
        </aop:aspect>
    </aop:config>
</beans>
```

5）测试类

```java
import com.rootpackage.services.service1;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations ="classpath:beans.xml" )
public class test1 {
    @Autowired
    service1 sv1;

    @Test
   public void test1(){

       sv1.doservice ();
   }
}

```

`3`execution指定切点的格式

```
访问修饰符 返回值 包名 类名 .方法名(参数类型)
```

| 格式       | 通配符 | 说明                                                       |
| ---------- | ------ | ---------------------------------------------------------- |
| 访问修饰符 | *      |                                                            |
| 返回值     | *      |                                                            |
| 包名       | *，..  |                                                            |
| 类名       | *      |                                                            |
| 方法名     | *      |                                                            |
| 参数类型   | *，..  | 直接写数据类型如int,java.lang.String，可有参数，也可无参数 |

全通配`* *..*.*(..)`，但通常情况都不用全同配。切入到业务实现类的所有方法，会有一个问题就是通知内容会多执行一次，是由于构造方法导致的。

`4`四种通知类型

| 关键字          | 类型 |
| --------------- | ---- |
| before          | 前置 |
| after-returning | 后置 |
| after-throwing  | 异常 |
| after           | 最终 |

`5`配置共用切入点

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="service1" class="com.rootpackage.services.service1"></bean>
    <bean id="log" class="com.rootpackage.mout.log"></bean>
    <aop:config>
        <aop:pointcut id="s1" expression="execution(* * ..*.doservice(..))"></aop:pointcut>
        <aop:aspect id="asp1" ref="log">
            <aop:before method="dolog1" pointcut-ref="s1"></aop:before>
            <aop:after-returning method="dolog2" pointcut-ref="s1"></aop:after-returning>
            <aop:after-throwing method="dolog3" pointcut-ref="s1"></aop:after-throwing>
            <aop:after  method="dolog4" pointcut-ref="s1"></aop:after>
        </aop:aspect>
    </aop:config>
</beans>
```

`6`环绕通知

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="service1" class="com.rootpackage.services.service1"></bean>
    <bean id="log" class="com.rootpackage.mout.log"></bean>
    <aop:config>
        <aop:pointcut id="s1" expression="execution(* * ..*.doservice(..))"></aop:pointcut>
        <aop:aspect id="asp1" ref="log">
            <aop:around method="doarround" pointcut-ref="s1"></aop:around>
        </aop:aspect>
    </aop:config>
</beans>
```

配置环绕通知后，只有通知方法执行，切入点原方法未执行。此时需要在通知方法中传入ProceedingJoinPoint的实现类（定义该接口的参数，spring自动传入实现类），再通过该实现类调用切入点方法

```java
package com.rootpackage.mout;

import org.aspectj.lang.ProceedingJoinPoint;

public class log {
    public void doarround(ProceedingJoinPoint pjp){
        try {
            System.out.println ("打印日志1");
            Object[] args=pjp.getArgs ();//获取切入点参数
            pjp.proceed (args);//调用切入点方法
            System.out.println ("打印日志2");
        } catch (Throwable throwable) {
            throwable.printStackTrace ( );
            System.out.println ("打印日志3");
        } finally {
            System.out.println ("打印日志4");
        }
    }
}
```

---

`7`注解

1）Aspect注解

表示该类是一个切面内

2）Before注解

表示该方法是一个前置通知

3）After注解

表示该方法是一个最终通知

4）AfterReturning注解

表示该方法是一个后置通知

5）AfterThrowing注解

表示该方法是一个异常通知

6）Arround注解

表示该方法是一个环绕通知

7）Point注解

定义一个切点，定义在一个方法上，参数是“execution(...)”，引用切面是需要调用该方法

在定义切面、通知前需要在配置文件中配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="service1" class="com.rootpackage.services.service1"></bean>
    <bean id="log" class="com.rootpackage.mout.log"></bean>
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy><!--需要配置这一句-->
</beans>
```

```java
package com.rootpackage.mout;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;


@Aspect//标识该类是一个切面类
public class log {

    @Pointcut("execution(* *..*.doservice(..))")//定义切点
    public void pf(){}

    @Before ("pf()")//定义前置通知
    public void dolog1(){
        System.out.println ("打印日志1");
    }
    @AfterReturning("pf()")
    public void dolog2(){
        System.out.println ("打印日志2");
    }
    @AfterThrowing("pf()")
    public void dolog3(){
        System.out.println ("打印日志3");
    }
    @After ("pf()")
    public void dolog4(){
        System.out.println ("打印日志4");
    }
}

```

执行后发现最终通知在后置通知之前执行，不合理。所以建议使用环绕通知

```java
package com.rootpackage.mout;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;


@Aspect//标识该类是一个切面类
public class log {

    @Pointcut("execution(* *..*.doservice(..))")//定义切点
    public void pf(){}
    
	@Around ("pf()")
    public void doarround(ProceedingJoinPoint pjp){
        try {
            System.out.println ("打印日志1");
            Object[] args=pjp.getArgs ();
            pjp.proceed (args);
            System.out.println ("打印日志2");
        } catch (Throwable throwable) {
            throwable.printStackTrace ( );
            System.out.println ("打印日志3");
        } finally {
            System.out.println ("打印日志4");
        }
    }
}

```

9）不使用xml配置

此时应该在配置类上加一行注解`@EnableAspectJAutoProxy`

`8`转账案例使用切面新增事务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="org.zhanghuan"></context:component-scan>
    <bean id="ds" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/test2?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8"></property>
        <property name="user" value="root"></property>
        <property name="password" value=""></property>
    </bean>
    <bean class="org.apache.commons.dbutils.QueryRunner">
        <constructor-arg name="ds" ref="ds"></constructor-arg>
    </bean>
    <aop:config><!--将事务通过切面织入-->
        <aop:pointcut id="p1" expression="execution(* org.zhanghuan.service.AccountServiceImpl.*(..))"></aop:pointcut>
        <aop:aspect id="isp1" ref="transaction">
            <aop:before method="beginTransaction" pointcut-ref="p1"></aop:before>
            <aop:after method="release" pointcut-ref="p1"></aop:after>
            <aop:after-returning method="commit" pointcut-ref="p1"></aop:after-returning>
            <aop:after-throwing method="rollback" pointcut-ref="p1"></aop:after-throwing>
        </aop:aspect>
    </aop:config>
    </beans>
```

`9`DataSourceTransactionManager的使用

Spring的事务处理中，通用的事务处理流程是由抽象事务管理器AbstractPlatformTransactionManager来提供的，而具体的底层事务处理实现，由PlatformTransactionManager的具体实现类来实现

1）配置文件方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <context:component-scan base-package="org.zhanghuan"></context:component-scan>
    <bean id="ds" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/test2?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8"></property>
        <property name="user" value="root"></property>
        <property name="password" value=""></property>
    </bean>
    <bean class="org.apache.commons.dbutils.QueryRunner">
        <constructor-arg name="ds" ref="ds"></constructor-arg>
    </bean>

    <!--导入事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager"><!--这个包在spring-jdbc中，需在maven中导入-->
        <property name="dataSource" ref="ds"></property>
    </bean>

    <!--配置事务通知-->
    <tx:advice id="manager" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="*" propagation="REQUIRED" read-only="false"></tx:method>
            <tx:method name="*find*" propagation="SUPPORTS" read-only="true"></tx:method><!--表示包含find关键字的方法，只读，不一定包含事务-->
        </tx:attributes>
    </tx:advice>

    <!--配置aop，将事务通知织入-->
    <aop:config>
        <aop:pointcut id="p1" expression="execution(* org.zhanghuan.service.AccountServiceImpl.*(..))"></aop:pointcut>
        <aop:advisor advice-ref="manager" pointcut-ref="p1"></aop:advisor>
    </aop:config>
    </beans>
```

事务通知里方法的属性

| 属性名          | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| isolation       | 事务隔离级别                                                 |
| propagation     | 指定事务传播形式，默认REQUIRED，表示必有事务。对于查询的方法，使用SUPPORTS |
| read-only       | 指定事务是否只读，默认false，查询方法设置未true              |
| timeout         | 指定事务超时时间，默认未-1，表示永不超时。如果设置值，以秒未单位 |
| rollback-for    | 指定一个异常，该异常发生，则回滚；其他异常发生，不回滚。无默认值，未设置表示出现任何异常都回滚 |
| no-rollback-for | 指定一个异常，该异常发生，不回滚；其他异常发生，回滚。无默认值，未设置表示出现任何异常都回滚 |

2）注解配置方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <context:component-scan base-package="org.zhanghuan"></context:component-scan>
    <bean id="ds" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/test2?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8"></property>
        <property name="user" value="root"></property>
        <property name="password" value=""></property>
    </bean>
    <bean class="org.apache.commons.dbutils.QueryRunner">
        <constructor-arg name="ds" ref="ds"></constructor-arg>
    </bean>

    <!--导入事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager"><!--这个包在spring-jdbc中，需在maven中导入-->
        <property name="dataSource" ref="ds"></property>
    </bean>

    <!--开启spring对注解事务的支持-->
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>

    <!--在需要开启事务的地方表上注解@Transactional-->
    </beans>
```

在配置文件中完成以上配置后，在需要开启事务的地方表上注解@Transactional

3）无xml方式

```java
//配置文件
package org.zhanghuan.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;

@Configuration
@Import (jdbcConfig.class)
@ComponentScan("org.zhanghuan")
@EnableTransactionManagement//相当于xml中的tx:annotation-driven标签+@Transactional注解
public class springConfig {
    @Bean//导入事务管理器
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource ds){
        return new DataSourceTransactionManager(ds);
    }
}
```

找到事务实现类，并开启事务

---

**JDBCTemplate**

`1`pom.xml导入依赖

```
		<dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
```

`2`快速使用

```java
//部分代码 		
 		//创建数据源
       DriverManagerDataSource ds=new DriverManagerDataSource ();
       ds.setDriverClass ("com.mysql.jdbc.Driver");
       ds.setJdbcUrl ("jdbc:mysql://127.0.0.1:3306/test2?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8");
       ds.setUser ("root");
       ds.setPassword ("");

       JdbcTemplate template=new JdbcTemplate (ds);
       List<Map<String,Object>> list=template.queryForList ("select * from account");
       for (Map<String,Object> m:
            list) {
           System.out.println ("id:"+m.get ("id"));
       }
```

`3`query方法

```java
query(String sql,Objectp[] args,RowMapper<T> rowmapper);//所有版本可用

query(String sql,RowMapper<T> rowmapper,Object.. args);//jdk1.5之后可用
```

---


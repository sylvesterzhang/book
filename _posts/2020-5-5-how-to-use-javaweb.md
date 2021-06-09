---
layout: post
title: JAVAWEB
tags: java
categories: backends
---
测试、反射、注解、Mysql、JDBC、Tomcat、servlet、HTTP、EL表达式、JSTL、Filter过滤器、代理模式、监听器、Redis、Maven

**测试**

`1`黑白盒测试

黑盒测试：不需要写代码，给输入值，看程序能否给出期望的输出值

白盒测试：需要写代码，关注程序具体执行流程

`2`测试步骤

1）定义测试类

包名：cn.itcast.test

类名：CaculatorTest

2）定义测试方法

方法名：test测试方法名

返回值：void

参数：空

3）使用注解@Test

执行后结果为红色，执行失败；绿色，执行成功。

4）断言

Assert.assertEquals(期望值，结果值)

5）初始化方法和释放资源方法

```javajava
@Before//修饰的方法在测试方法之前执行
public void init(){}
```

```
@After//修饰的方法在测试方法之后执行，一般用于释放资源
public void close(){}
```

---

**反射**

将类的各部分封装为其他类对象，多用于框架（半成品软件，简化编码）的开发。优势在于，一是可以在程序运行过程中操作这些对象，二是解耦程序，提高程序可扩展性

`1`获取class对象的方式

1）class.forName(全类名)

多用于配置文件

2）类名.class

多用于参数传递

3）对象.getClass()

多用于对象

同一字节码文件在异常程序运行过程中，只会被加载一次，不论通过那只方式获取的Class对象都是同一个

`2`Class对象的功能

1）获取成员变量

```
//获取public修饰的成员变量
public Field[] getFields() throws SecurityException;
public Field getField(String name);

//获取所有声明的成员变量
public Field[] getDeclaredFields() throws SecurityException;
public Field getDeclaredField(String name);
```

2）获取构造方法

```
public Constructor<?>[] getConstructors() throws SecurityException;
public Constructor<T> getConstructor(Class<?>... parameterTypes);
public Constructor<?>[] getDeclaredConstructors() throws SecurityException ;
public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes);
```

3）获取成员方法

```
public Method[] getMethods() throws SecurityException；
public Method getMethod(String name, Class<?>... parameterTypes)；
public Method[] getDeclaredMethods() throws SecurityException；
public Method getDeclaredMethod(String name, Class<?>... parameterTypes)；
```

4）获取类名

```
public String getName()；
```

5）获取、改变成员变量的值

```
public Object get(Object obj) throws IllegalArgumentException, IllegalAccessException;
```

```
public void set(Object obj, Object value);
```

6）创建对象

(1)构造方法对象创建有参实例对象

```
public T newInstance(Object ... initargs);
```

(2)类对象创建实例无参对象

```
public T newInstance();
```

```java
public class Person{
    public String name;
    private int age;

    public Person() {
        super();
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public void say(){
        System.out.println("我是"+this.name+",今年"+this.age+"岁了");
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

}
```

```java
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.Arrays;

public class Hello{
    public static void main(String[] args) throws NoSuchFieldException, NoSuchMethodException, IllegalAccessException, InstantiationException, InvocationTargetException {
        //获取类对象
        Class ps=Person.class;

        //获取类名
        System.out.println(ps.getName());

        //获取声明的成员变量
        Field[] fields=ps.getDeclaredFields();
        Field name=ps.getDeclaredField("name");
        Field age=ps.getDeclaredField("age");
        System.out.println(Arrays.toString(fields));
        System.out.println(name);
        System.out.println(age);

        //获取声明的构造方法
        Constructor[] c=ps.getDeclaredConstructors();
        System.out.println(Arrays.toString(c));
        Constructor c1=ps.getDeclaredConstructor(String.class,int.class);
        System.out.println(c1);

        //获取声明的成员方法
        Method[] mt=ps.getMethods();
        System.out.println(Arrays.toString(mt));
        Method m1=ps.getMethod("setAge", int.class);
        System.out.println(m1);
        Method m2=ps.getMethod("say");
        System.out.println(m2);

        //创建对象
        Object person=c1.newInstance("zhang",22);//构造方法对象创建有参对象
        System.out.println(person);
        Object person1=ps.newInstance();//类对象创建无参对象
        System.out.println(person1);

        //通过成员变量对象获取成员变量值
        Object name_value=name.get(person);
        System.out.println(name_value);
        age.setAccessible(true);//忽略访问权限修饰符的安全检测，无此语句会抛出错误
        Object age_value=age.get(person);//age是私有属性
        System.out.println(age_value);

        //通过成员变量对象设置成员变量值
        name.set(person,"zh");
        System.out.println(person);

        //通过成员方法对象执行对象的成员方法
        m2.invoke(person);
    }

}
```

`2`手写框架案例

要求：不改变类的任何代码的前提下，创建任意类对象，并执行其中的方法

```java
import java.io.IOException;
import java.io.InputStream;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.Properties;

public class Hello{
    public static void main(String[] args) throws IOException, ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        //读取配置文件
        Properties pro=new Properties();
        ClassLoader cl=Hello.class.getClassLoader();
        InputStream is=cl.getResourceAsStream("pro.Properties");
        pro.load(is);
        System.out.println(pro);//{ClassName=aaa.Person, MethodName=say}
        
        //创建类及对象，并执行方法
        Class ps = Class.forName(pro.getProperty("ClassName"));
        Constructor c = ps.getConstructor(String.class,int.class);
        Object person=c.newInstance("zhanghuan",22);
        Method m=ps.getMethod(pro.getProperty("MethodName"));
        m.invoke(person);//我是zhanghuan,今年22岁了
    }

}
```

---

**注解**

JDK1.5后的新特性，对元素进行说明注释，用于编译检查、生成doc文档、代码分析

`1`自带注解

@Override

检测被该注解标注的方法是否是继承自父类（接口）

@Deprecated

该注解标注的内容，表示已过时

@SuppressWarnings

压制警告，可传参数all，如@SuppressWarnings("all")

`2`自定义注解

1）格式

```
元注解
Public @interface MyAnno{

}
```

2）本质

注解的本质是一个接口，将其编译后再进行反编译，发现其是一个继承自java.lang.annotation.Annotation的接口

3）属性

指接口中定义的抽象方法，具有返回值

(1)返回值类型

int，String，枚举，注解及以上数据类型的数组

(2)属性赋值

使用属性前需要给属性赋值，设置default默认值的可不赋值

只有一个属性需要赋值，且属性名是value，可省略value并直接赋值

数组赋值时，需用{}包裹，只有一个值时，{}可省略

4）元注解

对注解进行注释

(1)@Target

描述注解的作用范围，参数分别是ElementType.TYPE类，ElementType.METHOD方法，ElementType.FIELD成员变量

(2)@Retention

注解被保留的阶段，参数常用RetentionPolicy.RUNTIME

(3)@Documented

注解是否被抽取到api文档中

(4)@Inherited

阐述了某个被标注的类型是被继承的

`3`获取类的注解

获取到类的class对象时，调用该对象的getAnnotation(注释名.class)方法

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE,ElementType.METHOD,ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnno {
    public String name();
    public int age();
}
```

```java
@MyAnno(name="zhanghuan",age=22)
public class Hello{
    public static void main(String[] args) {
        Class<Hello> hello=Hello.class;
        MyAnno an= hello.getAnnotation(MyAnno.class);
        //此方法相当于创建一个MyAnno的子类实现对象
        /*类似于以下的类
            class abc implements MyAnno{
                String name;
                int age;
                public abc(String name, int age) {
                    this.name = name;
                    this.age = age;
                }
                @Override
                public String name() {
                    return this.name;
                }
                @Override
                public int age() {
                    return this.age;
                }
            }
         */
        System.out.println(an.name());//zhanghuan
        System.out.println(an.age());//zhanghuan
    }

}
```

`4`测试案例

```java
//定义注解
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Check {
}
```

```java
//定义类，并在需要测试的方法上标注注解
public class Calculator {
    @Check
    public static int add(int a,int b){
        return a+b;
    }
    @Check
    public static int subtract(int a,int b){
        return a-b;
    }
    @Check
    public static int multiply(int a,int b){
        return a*b;
    }
    @Check
    public static int divide(int a,int b){
        return a/b;
    }
}
```

```java
//定义测试类
public class TestCalculator {
    public static void main(String[] args) throws ClassNotFoundException, IOException {
        Class cal=Class.forName("aaa.Calculator");
        Method[] m=cal.getMethods();
        BufferedWriter bw=new BufferedWriter(new FileWriter("bug.txt"));
        int num=0;
        for (Method method:
             m) {
            if(method.isAnnotationPresent(Check.class)){
                try {
                    Object result = method.invoke(cal.getInterfaces(), 10, 0);
                    System.out.println(result);
                }catch (Exception e){
                    num++;
                    bw.write(method.getName()+"()方法出异常了");
                    bw.newLine();
                    bw.write(e.toString());
                    bw.newLine();
                    bw.write("___________________________________");
                    bw.newLine();
                }
            }
        }
        bw.write("一共测出了"+num+"个异常");
        bw.close();
    }
}
```

---

**Mysql**

`1`数据文件删除

在mysql的安装目录可找到init文件，打开后找到有一行为`datadir="c:\ProgrData\MySql\MySqlServer5.5\"`，找到该路径，删除里面的文件即可

`2`常用命令

```
//打开服务窗口
Service.msc

//停止服务
net stop mysql

//开启服务
net start mysql

//登陆服务器
mysql -h127.0.0.1 -uroot -p

//登陆服务器方式二
myql --host=ip --user=root --password=abc

//退出mysql
exit
```

`3`SQL结构化查询语言

操作所有关系型数据库的规则

1）语法规则

(1)SQL语言可单行、多行书写，以分号结尾

(2)使用空格和缩进来则鞥去语句的可读性

(3)mysql的SQL语言不区分大小写，建议关键词大写

(4)注释，--或#为单行注释，其中#为mysql独有；/**/为多行注释

2）分类

(1)DDL（Data Defining langrage）定义语言

定义数据库对象：数据库，表，列等，如crete，drop，alter

(2)DML（Data Manipulation langrage）操作语言

对表中数据进行增、删、改，如insert，delete，update

(1)DQL（Data Query langrage）查询语言

查询表中记录，如select where

(1)DCL（Data Control langrage）控制语言

定义访问权限和安全级别，如GRANT，REVOKE

`5`对数据库的操作

1）创建

```
//创建一个名为db1的数据库
create database db1;

//创建一个数据库db1（如果db1不存在才创建）并设置字符集为gbk
create database if not exists db1 character set gbk;
```

2）查询

```
//查看所有数据库
show databases;

//查看数据库db1的创建语句
show create database db1;
```

3）修改

```
//将数据库db1的字符集修改为utf8
alter database db1 character set utf8;
```

4）删除

```
//删除数据库db1
drop database db1;

//删除数据库db1（如果存在才删除）
drop database if exists db1;
```

5）使用数据库

```
//查看正在使用的数据库
select database();

//使用数据库db1
use db1;
```

`6`对表的操作

1）创建

```
//创建一个名为student的表
create table student(
	name vachar(20),
	age int(2)
);
//复制表student
create table stu like student;
```

数据类型

| 数据类型  | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| int       | 整数，有符号的整数范围在正负21亿之间。声明时需要传入一个参数size，即整数的长度 |
| double    | 双精度浮点数。声明时需要传入两个参数，分别指小数点左侧数的长度和小数点右侧数的长度 |
| varchar   | 可变长度字符串，长度不超过255个字符。声明时需要传入字符串的长度 |
| date      | 包含年月日的日期                                             |
| datetime  | 包含年月日时分秒的日期                                       |
| timestamp | 包含年月日时分秒的时间戳，插入数据时会自动截取当前时间存入   |

2）查询

```
//查询所有表
show tables;

//查询表结构
dest student;
```

3）修改

(1)修改表名

```
alter table 表名 rename to 新表名;
```

(2)修改字符集

```
alter table 表名 character set 字符集名称;
```

(3)添加列

```
alter table 表名 add 列名 数据类型;
```

(4)修改列名称及其数据类型

```
alter table 表名 modify 列名 新列名 新数据类型;
```

(5)删除列

```
alter table 表名 drop 列名;
```

4）删除

```
//删除表student
drop table student

//删除表student（如果存在才删除）
drop table if exists student
```

`7`对数据操作

1）添加数据

```
insert into 表名(列名1,列名2) values(值1,值2);
```

>注意
>
>1.列名和值必须一一对应
>
>2.表名后不写列名，表示给所有列增加值
>
>3.除数字外吗，其他类型的值都需要用引号包裹

2）删除数据

```
delet form 表名 [where条件]
```

> 注意
>
> 1.如果不加条件，表示删除表中所有数据
>
> 2.如果要删除表中所有数据，推荐使用`TRUNCATE TABLE 表名`，效率高，其原理是删除表后再重新创建一张同名表

3）修改数据

```
update 表名 set 列名1=值1,列名2=值2 [where条件]
```

> 注意
>
> 如果不加条件，表中所有数据将会被修改

`7`查询表中数据

1）语法

```
select
	字段1,字段2……
from 
	表1,表2……
where
	条件列表
goroup by
	分组字段
having
	分组之后条件
order by
	排序
limit
	分页
```

2）基础查询

(1)去除重复结果

```
SELECT DISTINCT adress from student;
```

(2)计算列

```
SELECT name,math,english,math+IFNULL(english,0) FROM student;
```

IFNULL(english,0)是指如果english为null，当作0来处理

(3)起别名

```
//AS来替代字段名进行显示，AS可省略
SELECT name AS 姓名,math AS 数学,english AS 英语,math+IFNULL(english,0) AS 总分 FROM student;
```

(4)条件查询

除了常规的运算符

```
<,>,>=,<=,=,!=,<>
```

还有

```
//BWTWEEN AND
SELECT name,math,english FROM student where math BETWEEN 70 AND 80;
//AND
SELECT name,math,english FROM student where math>70 AND math<80;
//OR
SELECT name,math,english FROM student where math<70 OR math>80;
//IN
SELECT name,math,english FROM student where math IN (50,52,64);
```

如果数据内容是null，则不可以用判断

```
//找出english是null的数据
SELECT name,math,english FROM student where english IS NULL;
//找出english不是null的数据
SELECT name,math,english FROM student where english IS NOT NULL;
```

模糊查询LIKE

```
SELECT name,math,english FROM student where name LIKE "%杰_";
/*
%是多个字符的占位符
_是单个字符的占位符
*/
```

(5)排序查询

```
//将结果math进行默认排序(升序)
SELECT name,math,english FROM student ORDER BY math;
//将结果math进行升序排列
SELECT name,math,english FROM student ORDER BY math ASC;
//将结果math进行降序排列
SELECT name,math,english FROM student ORDER BY math DESC;
```

> 注意
>
> 1.默认按升序排列
>
> 2.有多个排序条件，则前面条件值一样时，才会使用后面的排序条件

(6)聚合函数

将一列数据作为一个整体，纵向计算。包含COUNT,MAX,MIN,SUM,AVG

```
//计数
SELECT COUNT(name) FROM student;
//求最大值
SELECT MAX(math) FROM student;
//求最小值
SELECT MIN(math) FROM student;
//求和
SELECT SUM(math) FROM student;
//求平均
SELECT AVG(math) FROM student;
```

> 注意
>
> 聚合函数计算时会排除掉值为null的数据

(7)分组查询

一般和聚合函数合用

```
SELECT AVG(math) FROM student GROUP BY male;
```

where和having的区别

```
/*
1.where在执行分组操作前限定，having在执行分组操作后限定
2.where后不可跟聚合函数
3.having子句中可以使用字段别名，而where不能使用
*/

SELECT SUM(math) AS 数学 FROM `student` GROUP BY male HAVING 数学>150;
```

(8)分页

```
LIMIT 开始索引,每页条数
```

公式：开始索引=(当前页码-1)*每页条数

只有mysql的分页是limit，其他数据库软件不一样

`8`约束

对表中数据进行限定，保证数据的正确性、有效性、完整性。分为：

主键约束(primarykey)、非空约束(notnull)、唯一约束(unique)、外键约束(foreignkey)

1）非空约束

```
//创建表时新增索引
CREATE TABLE stu(
	id int NOTNULL,
	name varchar(20)
)

//建表后新增索引
ALTER TABLE stu MODIFY name VARCHAR(20) NOTNULL;

//删除索引
ALTER TABLE stu MODIFY name VARCHAR(20);
```

2）唯一索引

```
//新增索引
ALTER TABLE stu MODIFY id INT UNIQUE;

//新增索引方式二
ALTER TABLE stu ADD UNIQUE(id);

//删除索引
ALTER TABLE stu DROP INDEX id;
```

唯一索引里的值可以为多个null

3）主键索引

```
//新增
ALTER TABLE student MODIFY id INT PRIMARY KEY;

//删除
ALTER TABLE student DROP PRIMARY KEY;

//新增自增
ALTER TABLE student MODIFY id INT AUTO_INCREMENT;

//删除自增
ALTER TABLE student MODIFY id INT;
```

非空且唯一

4）外键约束

```
//创建方式一
CREATE TABLE employee(
	dep_id INT,
	CONSTRAINT enp_dep_fk FOREIGN KEY (dep_id) REFERENCES department(id)
);
//需要注意，department.id必须是主键，否则报错;外键名enp_dep_fk可省略

//创建方式二
ALTER TABLE employee ADD CONSTRAINT enp_dep_fk FOREIGN KEY (dep_id) REFERENCES department(id);

//删除外键
ALTER TABLE employee DROP FOREIGN KEY enp_dep_fk;

//创建带级联的外键
ALTER TABLE employee ADD CONSTRAINT enp_dep_fk FOREIGN KEY (dep_id) REFERENCES department(id) ON UPDATE CASCADE ON DELETE CASCADE;
```

`9`数据库设计的范式

1）函数依赖

A属性值可确定唯一B属性值，则B依赖于A

2）完全依赖

A为属性组，B属性值的确定需依赖A属性组中所有的属性值

3）部分依赖

A为属性组，B属性值的确定需依赖A属性组中部分的属性值

4）传递依赖

C属性值依赖于B属性值，B属性值依赖于A属性值，那么C属性值传递依赖于A属性值

5）码

属性或属性组被其他属性完全依赖，那么这个属性或属性组称为码

6）主属性和非主属性

码是主属性，其他的属性是非主属性

| 范式     | 特点                                                         |
| -------- | ------------------------------------------------------------ |
| 第一范式 | 数据冗余，存在函数依赖，出现新增异常和删除异常               |
| 第二范式 | 消除非主属性对主属性的部分函数依赖，但仍出现新增异常和删除异常 |
| 第三范式 | 所有主属性不依赖于其他非主属性，消除传递依赖                 |

`10`备份和还原

1）备份

```
mysql dump -u用户名 -p密码 数据库名称 >路径
```

2）还原

```
//登陆数据库、创建数据库、使用数据库

//执行命令,其中a.sql是备份的数据库文件
source a.spl
```

`9`多表查询

1）笛卡儿积

两个集合A、B，取这两个集合的所有组合情况

2）隐式内联接

```
SELECT
	t1.time,t1.gender,t1.name
FROM
	emp t1,dep t2
WHRER
	t1.dept_id=t2.id;
```

内联接查询的是两张表的交集

3）显式内联接

```
SELECT * FROM exp INNER JOIN dept ON emp.dep_id=dept.id;
```

4）左外联接

```
SELECT 字段列表 FROM 表1 LEFT JOIN 表2 ON 条件;
```

查询的是左表所有的数据交集部分

5）右外联接

```
SELECT 字段列表 FROM 表1 RIGHT JOIN 表2 ON 条件;
```

查询的是右表所有的数据交集部分

6）子查询

(1)子查询结果单列、单行

```
SELECT * FROM emp WHERE salary=(SELECT MAX(salary) FROM emp);
```

(2子查询结果单列、多行

```
SELECT * FROM emp WHERE id IN (SELECT id FROM emp WHERE salary>5000);
```

(3)子查询结果多列、多行

```
SELECT
	* 
FROM
	(SELECT * FROM emp WHERE salary>5000) t1,dep t2 
WHERE
	t1.dept_id=t2.id;
```

`10`事务

一个包含多个步骤的操作，要么成功，要么失败

```
START TRANSACTION;//开启事务
……
COMMIT;//提交
```

1）事务提交的两种方式

(1)自动提交

mysql默认自动提交，一个增、删、改操作时，会自动提交一次事务

(2)手动提交

需开启事务再提交,oracle默认是手动提交

2）修改事务提交的方式

(1)查看

```
SELECT @@autocommit;
```

1为自动提交，0为手动提交

(2)修改

```
SET @@autocommit=0;
```

3）事务的四大特征

原子性：不可分割的最小单位，要么成功要么失败

持久性：提交后会持久性保存

隔离性：事务之间相互独立

一致性：数据操作前后总量不变

4）事务隔离级别

(1)事务操作存在的问题

脏读：一个事务读取到另一个事务没有提交的数据

不可重复读：同一事务，两次读到的数据不同

幻读：一个事务操作数据表中的所有记录，另一个事务添加一条数据，则第一个事务读不到自己的修改

(2)隔离级别

| 隔离级别                   | 存在问题               |
| -------------------------- | ---------------------- |
| read uncommitted           | 脏读、不可重复读、幻读 |
| read committed(oracle默认) | 不可重复读、幻读       |
| repeatable(mysql默认)      | 幻读                   |
| serializable               | 无                     |

级别从小到大，安全性越来越高，效率越来越低

(3)查询隔离级别

```
SELECT @@tx_isolation;
```

(4)设置隔离级别

```
SET GLOBAL TRANSACTION ISOLATION LEVAL 级别字符串;
```

`11`管理用户

1）查看用户

```
USE mysql;
SELECT host,user,password FROM user;
```

2）创建用户

```
CREATE user '用户名'@'主机名' IDENTIFIED BY '密码';
```

3）删除用户

```
DROP user '用户名'@'主机名';
```

4）修改密码

```
UPDATE user SET password=PASSWORD('新密码') WHERE user='用户名';
```

5）忘记root密码

(1)停止mysql，net stop mysql;

(2)使用无验证方式启动mysql，mysql --skip -grant-tables

(3)在cmd中输入mysql，无需输入账号密码进入

(4)改密码

(5)重启mysql

6）查看权限

```
SHOW GRANTS FOR '用户名'@'主机名';
```

7）授予权限

```
GRANT 权限列表 ON 数据库名,表名 TO '用户名'@'主机名';

//授予全部权限
GRANT All ON *.* TO 'zhanghuan'@'%';
```

权限分别是select,delete,update,all

8）撤销权限

```
REVOKE 权限列表 ON 数据库名,表名 FROM '用户名'@'主机名';
```

---

**JDBC**

简单来说，java语言操作数据库。官方定义一套操作所有关系型数据库的规则（接口），由各个数据库厂商实现这套接口，提供给开发者驱动数据库的jar包。

`1`快速入门

(1)导入jar包

复制jar包到新建的libs文件夹，add as library

(2)注册驱动

(3)获取数据库连接对象Connection

(4)定义sql

(5)获取执行sql语句的对象statement

(6)执行sql，接收返回结果

(7)处理结果

(8)释放资源

`2`常用对象

| 对象              | 名             |
| ----------------- | -------------- |
| DriverMannager    | 驱动管理对象   |
| Connection        | 数据库连接对象 |
| Statement         | 执行sql的对象  |
| ResultSet         | 结果集对象     |
| PreparedSteatment | 执行sql的对象  |

`3`DriverMannager

(1)注册驱动

```
//driver类的成员方法
static void registerDriver(Driver driver);
```

mysql5之后的驱动可省略注册

```
//源码摘取自driver
static {
        try {
            DriverManager.registerDriver(new Driver());
        } catch (SQLException var1) {
            throw new RuntimeException("Can't register driver!");
        }
    }
```

```
//写入该句则完成注册
Class.forName("com.mysql.jdbc.Driver");
```

(2)获取数据库连接

```
static Connection getConnection(String url,String user,String password);
```

url格式 jdbc:mysql://localhost:3306/db3，连接本机数据库可省略localhost:3306

`4`Connection

(1)获取执行sql的对象

```
Statement createStatement();
PreparedStatement PrepareStatement(String sql);
```

(2)获取数据库连接

开启：setAutoCommit(boolean autoCommit)，参数欸false，开启事务

提交：commit()

回滚：rollback()

`5`Statement

(1)boolean execute(String sql)

执行任何sql语句，不常用

(2)int executeUpdate(String sql)

执行DML语句（insert,update,delete）。执行DDL时，返回值为0；返回值为影响的行数，可通过影响的行数判断是否执行成功，返回值>0执行成功，反之执行失败。

(3)ResultSet executeQuery(String sql)

执行DQL语句（select）

`6`ResultSet结果集对象

```
//游标向下移动一行，判断当前位置是否位于最后一行，如果是返回值false
boolean next();

//获取数据,可传入两种类型的参数，传入int类型时，该参数代表列号；传入String类型时，该参数代表列名
int getInt(参数);
String getString(参数);
```

```java
import java.sql.*;

public class TestCalculator {
    public static void main(String[] args) throws ClassNotFoundException {
        //注册
        Class.forName("com.mysql.jdbc.Driver");
        try (Connection con = DriverManager.getConnection("jdbc:mysql:///test1","root","")) {//连接数据库

            String sql="select * from student";
            //获取执行sql的对象
            Statement st=con.createStatement();
            //执行sql语句
            ResultSet rs=st.executeQuery(sql);
            while (rs.next()){
                System.out.println(rs.getString(1)+" "+rs.getString(2)+" "+rs.getString(3)+" "+rs.getString(4)+" "+rs.getString(5));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

`7`PreparedSteatment

在拼接sql时，当有一些sql特殊关键字参数与字符串拼接时，会造成安全问题，此时使用PreparedSteatment来解决

```
PreparedStatement Connection.PrepareStatement(String sql);
```

```java
import java.sql.*;

public class TestCalculator {
    public static void main(String[] args) throws ClassNotFoundException {
        //注册
        Class.forName("com.mysql.jdbc.Driver");
        try (Connection con = DriverManager.getConnection("jdbc:mysql:///test1","root","")) {//连接数据库

            String sql="select * from student where id=?";
            
            //获取执行sql的对象
            PreparedStatement st=con.prepareStatement(sql);
            //给问号赋值
            st.setString(1,"1");
            //执行sql语句
            ResultSet rs=st.executeQuery();
            while (rs.next()){
                System.out.println(rs.getString(1)+" "+rs.getString(2)+" "+rs.getString(3)+" "+rs.getString(4)+" "+rs.getString(5));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

`8`JDBCutils

(1)获取配置文件路径

```java
import java.net.URL;

public class JDBCutils {
    public static void main(String[] args) {
        ClassLoader cl=JDBCutils.class.getClassLoader();
        URL res=cl.getResource("jdbc.properties");//jdbc.properties文件必须在根目录下才可读出
        String path=res.getPath();
        System.out.println(path);// /C:/Users/admin/IdeaProjects/untitled1/out/production/untitled1/jdbc.properties
    }
}
```

(2)完整实现

```
//配置文件jdbc.properties
url=jdbc:mysql:///test1
user=root
password=
driver=com.mysql.jdbc.Driver
```

```java
//JDBCutils
import java.io.FileReader;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

public class JDBCutils {
    static  String url=null;
    static String user=null;
    static String password=null;
    static String driver=null;
    static{
        try {
            //获取配置文件路径
            ClassLoader cl=JDBCutils.class.getClassLoader();
            URL res=cl.getResource("jdbc.properties");//jdbc.properties文件必须在根目录下才可读出
            String path=res.getPath();

            //读取配置文件
            Properties p=new Properties();
            p.load(new FileReader(path));
            url=p.getProperty("url");
            user=p.getProperty("user");
            password=p.getProperty("password");
            driver=p.getProperty("driver");

            //注册驱动
            Class.forName(driver);
        }catch (Exception e){
            System.out.println(e);
        }
    }
    public static Connection getConnection(){//获取连接
        Connection con=null;
        try {
            con = DriverManager.getConnection(url, user, password);
        }catch (Exception e){
            System.out.println(e);
        }
        return con;
    }

    public static void close(ResultSet res, Statement st,Connection conn){//释放资源
        if(res!=null){
            try {
                res.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(st!=null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
//使用JDBCutils
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class Hello{
    public static void main(String[] args) {
        Connection con=JDBCutils.getConnection();
        PreparedStatement st=null;
        ResultSet rs=null;

        String sql="select * from student where id=?";
        try{
            st = con.prepareStatement(sql);
            st.setString(1,"2");
            rs=st.executeQuery();
            while (rs.next()){
                System.out.println(rs.getString(1)+" "+rs.getString(2)+" "+rs.getString(3)+" "+rs.getString(4));
            }
        }catch (Exception e){
            System.out.println(e);
        }finally {
            JDBCutils.close(rs,st, con);
        }
    }

}
```

`9`事务

```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class Hello{
    public static void main(String[] args) {
        Connection con=JDBCutils.getConnection();
        Statement st=null;
        ResultSet rs=null;


        try{
            con.setAutoCommit(false);//开启事务
            st = con.createStatement();
            int res1=st.executeUpdate("update employee set salary=salary+500 where id=1");
            System.out.println(res1);
            int res2=st.executeUpdate("update employee set salary=salary-500 where id=2");
            System.out.println(res2);
            con.commit();//提交事务
        }catch (Exception e){
            try {
                con.rollback();//回滚事务
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
            System.out.println(e);
        }finally {
            JDBCutils.close(rs,st, con);
        }
    }

}
```

`10`数据库连接池

当初始化后，容器被创建，容器会申请一些连接对象，当用户访问数据库时，从池中取出连接对象进行连接；用户访问完后，将连接对象归还给容器

(1)c3p0

```
//1.导入两个jar包，注意需先导入mysql驱动包
//2.定义配置文件，名称c3p0.properties或c3p0-config.xml，放在src即可
//3.获取连接对象new ComboPooledDataSource()
//4.获取连接getConnection
```

```java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

public class Hello{
    public static void main(String[] args) {
        DataSource ds = new ComboPooledDataSource();//创建数据库连接池
        Connection con=null;
        Statement st=null;
        ResultSet rs=null;
        try{
            con=ds.getConnection();//获取连接
            System.out.println(con);
            st=con.createStatement();
            rs=st.executeQuery("select * from student");
            while (rs.next()){
                System.out.println(rs.getString(1));
            }
        }catch (Exception e){
            System.out.println(e);
        }finally {
            JDBCutils.close(rs,st,con);
        }
    }

}
```

配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
    <!-- 这是默认配置信息 -->
    <default-config>
        <!-- 连接四大参数配置 -->
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/test1</property>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="user">root</property>
        <property name="password"></property>
        <!-- 池参数配置 -->
        <property name="acquireIncrement">3</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">2</property>
        <property name="maxPoolSize">10</property>
    </default-config>

    <!-- 专门为oracle提供的配置信息 -->
    <named-config name="oracle-config">
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/mydb1</property>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="user">root</property>
        <property name="password">123</property>
        <property name="acquireIncrement">3</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">2</property>
        <property name="maxPoolSize">10</property>
    </named-config>
</c3p0-config>
```

(2)druid

```
//1.导入jar包
//2.定义配置文件
//3.加载配置文件
//4.配置文件手动传入，并获取连接对象
//5.获取连接
```

```java
import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.Properties;

public class Hello{
    public static void main(String[] args) throws IOException {
        Properties p=new Properties();
        p.load(Hello.class.getClassLoader().getResourceAsStream("druid.properties"));
        try {
            DataSource ds = DruidDataSourceFactory.createDataSource(p);//创建连接对象
            Connection con=ds.getConnection();//获取连接
            Statement st=con.createStatement();
            ResultSet rs=st.executeQuery("select * from student");
            while (rs.next()){
                System.out.println(rs.getString(1));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```

封装工具类

```
# druid.properties文件的配置
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/test1
username=root
password=
# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大超时时间
maxWait=3000
```

```java
//DRUIDutils
import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;

public class DRUIDutils {
    static Properties p=new Properties();
    static{
        try {
            p.load(DRUIDutils.class.getClassLoader().getResourceAsStream("druid.properties"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static Connection getConnection(){//获取连接
        Connection con=null;
        try {
            con=getDataSource().getConnection();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return con;
    }
    public static DataSource getDataSource() throws Exception {//获取datasource
        return DruidDataSourceFactory.createDataSource(p);
    }
    public static void close(ResultSet res, Statement st, Connection conn){//释放资源
        if(res!=null){
            try {
                res.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(st!=null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    public static void close(Statement st, Connection conn){//释放资源重载方法
        if(st!=null){
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}
```

```java
//测试DRUIDutils
import java.io.IOException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

public class Hello{
    public static void main(String[] args) throws IOException {
        Connection con=null;
        Statement st=null;
        ResultSet rs=null;
        try {
            con=DRUIDutils.getConnection();//获取连接
            st=con.createStatement();
            rs=st.executeQuery("select * from student");
            while (rs.next()){
                System.out.println(rs.getString(1));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            DRUIDutils.close(rs,st,con);
        }
    }

}
```

(3)SpringJDBC

Spring框架对JDBC的简单封装，提供一个JDBCTemplate对象简化JDBC的代码

```
//1.导入jar包，共5个
//2.创建JdbcTemplate对象，依赖于数据源DataSource
JdbcTemplate template=new JdbcTemplate(ds)
//3.调用JdbcTemplate的方法进行CRUD操作
```

```java
import org.springframework.jdbc.core.JdbcTemplate;

public class Hello{
    public static void main(String[] args) throws Exception {
        JdbcTemplate template=new JdbcTemplate(DRUIDutils.getDataSource());
        String sql="update employee set salary=5000 where id=?";
        int rs=template.update(sql,1);//执行sql
        System.out.println(rs);
    }

}
```

常用CRUD操作

```
update();//执行DML语句，增删改语句
queryFormap();//查询结果封装乘map集合，结果集长度只可是1
queryForList();//查询结果封装成list集合
query();//查询结果封装成JavaBean对象
queryForObject();//查询结果封装成对象，查询聚合函数
```

```java
import org.junit.jupiter.api.Test;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;

import java.util.List;
import java.util.Map;

public class Hello{
    public static void main(String[] args) throws Exception {
        JdbcTemplate template=new JdbcTemplate(DRUIDutils.getDataSource());
        String sql=null;

        //queryForMap
        sql="select * from student where id=1";
        Map<String, Object> m=template.queryForMap(sql);
        System.out.println(m);
        //{id=1, male=男, name=周杰伦, math=78, english=81}

        //queryForList
        //将每一条记录封装成一个map集合，再将map集合封装到list集合中
        sql="select * from student";
        List<Map<String,Object>> list_of_map=template.queryForList(sql);
        System.out.println(list_of_map);

        //query
        //将数据封装成javabean对象并返回一个javabean对象的list集合
        sql="select * from student";
        List<student> list_of_bean=template.query(sql,new BeanPropertyRowMapper<student>(student.class));
        System.out.println(list_of_bean);

        //queryForObject
        //执行包含聚合函数的sql
        sql="select count(*) from student";
        Long count=template.queryForObject(sql,Long.class);
        System.out.println(count);
    }
    @Test//可单独运行方法
    public void run(){
        System.out.println("hello");
    }

}
```

---

**Tomcat**

`1`JAVAEE

java语言在企业计数规范的总和，一共规定了12项大的规范

`2`主流服务软件

| 软件名     | 特点             |
| ---------- | ---------------- |
| weblogic   | oracle公司，收费 |
| websophere | ibm公司，收费    |
| jboss      | jboss公司，收费  |
| tomcat     | apache组织，免费 |

`3`tomcat文件目录

| 目录    | 功能           |
| ------- | -------------- |
| bin     | 二进制执行文件 |
| conf    | 配置文件       |
| lib     | 依赖jar包      |
| logs    | 日志文件       |
| temp    | 临时文件       |
| webapps | 存放的web项目  |
| work    | 运行时数据     |

`4`启动和关闭

1）启动

bin目录下，运行startup.bat，在localhost:8080访问

可能遇到问题

(1)黑窗口一闪而过

未正确配置java环境变量JAVA_HOME

(2)启动报错

8080端口被占用，要么杀进程，要么在conf.server.xml修改端口号

2）关闭

bin目录下，运行shutdown.bat或者在服务窗口ctrl+c

`5`部署

1）将项目放到webapps目录下

项目可打包成war，将war包防止到webapps目录下

2）配置conf/server.xml文件

新增一行`<Contex docBase="D\hello" path"/hehe/">`

3）在conf/Catalina/localhost下创建任意名的xml文件

文件内容是`<Contex docBase="D\hello" >`，连接中访问的path路径就是xml的文件名

`6`动态项目的目录结构

```
--项目根目录
	--WEB-INF
	--web,xml//web项目的核心配置文件
	--classes//放置字节码文件的目录
	--lib//放置依赖的jar包
```

`7`将tomcat部署到IDEA中

1）配置tomcat

run->edit configrations->templates->tomcat server->local

把tomcat的安装路径设置上

2）创建JAVAEE项目

file->new project->java enterprise

选中web application后点击下一步

输入项目名点击下一步，然后运行

---

**servlet**

运行在服务器上的小程序，本质上是一个接口

`1`快速开始

```
1.创建javaee项目
2.定义类，实现Servlet
3.实现抽象方法
4.配置url，如配置web.xml文件
```

```java
//创建一个实现Servlet的类
import javax.servlet.*;
import java.io.IOException;

public class abc implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("service……");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}
```

```xml
//web.xml将新建的实现类注册进去，并配置url
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>demo01</servlet-name>
        <servlet-class>abc</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>demo01</servlet-name>
        <url-pattern>/demo01</url-pattern>
    </servlet-mapping>
</web-app>
```

`2`执行原理

1）服务器接收客户端浏览器的请求后，会解析请求的url路径，获取访问的servlet资源路径

2）查找web.xml文件是否由对应的<url-pattern>内容，如果由有将其对应的类加载到内存，并执行其方法

`3`servlet生命周期

1）被创建

创建后会执行一次实现类的init方法，默认情况是在第一次访问实现类对应的url时才会被创建，这里可以在web.xml文件中配置成服务器启动时创建

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>demo01</servlet-name>
        <servlet-class>abc</servlet-class>
        <load-on-startup>2</load-on-startup><!--正数或0表示服务启动时加载类，负数表示访问url时加载类-->
    </servlet>
    <servlet-mapping>
        <servlet-name>demo01</servlet-name>
        <url-pattern>/demo01</url-pattern>
    </servlet-mapping>
</web-app>
```

2）提供服务

每次调用servlet时，其service方法被执行一次

3）被销毁

在servlet被销毁前，执行destroy方法

总之，servlet是单例的，不要哦在servlet中定义变量，即使定义了，也不要修改值

`4`注解配置url

在servlet3.0中，支持注解配置url，不再需要web.xml

```java
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.IOException;

@WebServlet("/demo")//注解配置url
public class abc implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init……");
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("service……");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}
```

`5`IDEA中的tomcat

1）IDEA为每一个tomcat部署的项目单独建立一份配置文件

2）IDEA会将工作空间里的项目复制到tomcat上部署

3）WEB-INF目录下的资源无法被访问

4）端点调试，需要使用小虫子启动

`5`servlet的实现类

1）GenericServlet

该类是servlet的实现类，有一个抽象方法service

```java
//源码摘取
public abstract void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
```

2）HttpServlet

常需重写doGet和doPost方法

```java
//源码摘取
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String method = req.getMethod();
    long lastModified;
    if (method.equals("GET")) {
        lastModified = this.getLastModified(req);
        if (lastModified == -1L) {
            this.doGet(req, resp);
        } else {
            long ifModifiedSince;
            try {
                ifModifiedSince = req.getDateHeader("If-Modified-Since");
            } catch (IllegalArgumentException var9) {
                ifModifiedSince = -1L;
            }

            if (ifModifiedSince < lastModified / 1000L * 1000L) {
                this.maybeSetLastModified(resp, lastModified);
                this.doGet(req, resp);
            } else {
                resp.setStatus(304);
            }
        }
    } else if (method.equals("HEAD")) {
        lastModified = this.getLastModified(req);
        this.maybeSetLastModified(resp, lastModified);
        this.doHead(req, resp);
    } else if (method.equals("POST")) {
        this.doPost(req, resp);
    } else if (method.equals("PUT")) {
        this.doPut(req, resp);
    } else if (method.equals("DELETE")) {
        this.doDelete(req, resp);
    } else if (method.equals("OPTIONS")) {
        this.doOptions(req, resp);
    } else if (method.equals("TRACE")) {
        this.doTrace(req, resp);
    } else {
        String errMsg = lStrings.getString("http.method_not_implemented");
        Object[] errArgs = new Object[]{method};
        errMsg = MessageFormat.format(errMsg, errArgs);
        resp.sendError(501, errMsg);
    }

}
```

`6`url定义

1）/xx

2）/xx/xx

3）*.do

---

**HTTP**

定义的客户端与服务端通信的数据格式

特点：

基于TCP/IP协议，默认端口为80，基于请求、响应模型，无状态

版本

1.0每次请求、响应会重新建立连接，1.1复用连接

`1`请求消息的数据格式

1）请求行

2）请求头

3）请求空行

4）请求体

`2`GET和POST

GET请求的参数在请求行的url中，url里的数据长度有限制

POST请求的参数在请求体中，长度无限制

`3`请求头中的refer

标记该请求的跳转来源，用于防盗链和统计

`4`request对象基础体系结构

```
ServletRequest	接口
	|继承
HttpServletRequest	接口
	|继承
org.appache.catalina.connector.RequestFacade	tomcat创建的实现类
```

`5`request的功能

1）获取获取请求消息数据

```java
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/demo1")
public class abc extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1）获取请求方式
        String m=req.getMethod();
        System.out.println(m);//GET
        //2）获取虚拟目录
        String cp=req.getContextPath();
        System.out.println(cp);//
        //3)获取servlet路径
        String sp=req.getServletPath();
        System.out.println(sp);///demo1
        //4)获取get请求参数
        String qs=req.getQueryString();
        System.out.println(qs);//null
        //5）获取uri
        String uri=req.getRequestURI();
        StringBuffer url=req.getRequestURL();
        System.out.println(uri);//demo1
        System.out.println(url);//http://localhost:8081/demo1
        //6）获取协议版本
        String ptc=req.getProtocol();
        System.out.println(ptc);//HTTP/1.1
        //7）获取客户机ip地址
        String rma=req.getRemoteAddr();
        System.out.println(rma);//0:0:0:0:0:0:0:1
    }

}
```

2）获取请求头数据

```java
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

@WebServlet("/demo1")
public class abc extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Enumeration<String> headernames=req.getHeaderNames();
        while (headernames.hasMoreElements()){
            System.out.println(headernames.nextElement());//获取请求头的键
        }
        /*
        host
        connection
        cache-control
        upgrade-insecure-requests
        user-agent
        ……
         */
    }

}
```

```java
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

@WebServlet("/demo1")
public class abc extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String agent=req.getHeader("user-agent");//获取请求头某个键对应的值
        System.out.println(agent);
        //Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.113 Safari/537.36 Edg/81.0.416.62
    }

}
```

3）获取请求体数据

```java
import javax.servlet.*;
        import javax.servlet.annotation.WebServlet;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
import java.io.BufferedReader;
import java.io.IOException;

@WebServlet("/demo1")
public class abc extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("dopost....");
        BufferedReader br=req.getReader();//获取字符输入流
        char[] data=new char[1024];
        int len;
        while ((len=br.read(data))!=-1){
            System.out.println(data);
        }
        //username=2323&password=232323
    }


}
```

```java
import javax.servlet.*;
        import javax.servlet.annotation.WebServlet;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/demo1")
public class abc extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("dopost....");
        ServletInputStream sis=req.getInputStream();//获取字节输入流
        byte[] b=new byte[1024];
        int len;
        while ((len=sis.read(b))!=-1){
            System.out.println(new String(b));
        }
        //username=2323&password=232323
    }

}
```

4）其他方法

```java
import javax.servlet.*;
        import javax.servlet.annotation.WebServlet;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.Map;
import java.util.Set;

@WebServlet("/demo1")
public class abc extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取参数值
        String username=req.getParameter("username");
        System.out.println(username);//zhanghuan
        
        //获取参数值的数组
        String[] vls=req.getParameterValues("hobby");
        System.out.println(Arrays.toString(vls));//[basketball, football]
        
        //获取参数的键集合
        Enumeration<String> names=req.getParameterNames();
        while (names.hasMoreElements()){
            System.out.println(names.nextElement());
        }
        /*
        username
        password
        hobby
         */
        
        //获取参数的键值对
        Map<String,String[]> mp=req.getParameterMap();
        Set<Map.Entry<String, String[]>> set=mp.entrySet();
        for(Map.Entry<String,String[]> i:set){
            System.out.println(i.getKey()+"="+Arrays.toString(i.getValue()));
        }
        /*
        username=[zhanghuan]
        password=[2323]
        hobby=[basketball, football]
         */
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }
}

//不管是get方式还是post方式，这些获取参数的方法都生效
```

5）乱码问题

在tomcat8以上，get方式提交的值无乱码问题，而post方式提交的值存在乱码

需要在doPost方法里加上

```
req.setCharacterEncoding("utf-8");
```

6）请求转移

一种在服务器内部的资源跳转方式，因此浏览器的url不会发生变化，由于在服务器内部转移，所以只能转发到服务器内部的servlet对象，转发过程中是服务器自发与客户端浏览器无关

```
req.getRequestDispatcher("/demo2").forward(req,resp);
```

7）共享数据

域对象：一个有作用范围的对象，可在范围内共享数据

request域：一次请求范围，一般用于请求转发的多个资源中共享数据

```
//设置共享值
void setAttribute(String var1, Object var2);

//获取共享值
Object getAttribute(String var1);

//删除共享值
void removeAttribute(String var1);

//获取整个web应用的域对象
ServletContext getServletContext();
```

6）登陆案例

```
//目录结构
--src
	--dao
		userdao.java
	--doMain
		user.java
	--uitil
		JDBCutils.java
	--web
		loginServlet.java
	duoid.properties
--web
	--WEB-INFO
		--lib//这里放jar包,如果放错位置控制太不会报错，页面上会报500错误
	index.jsp
```

```java
//JDBCutils.java
import java.io.InputStream;
import java.sql.Connection;
import java.util.Properties;

public class JDBCutils {
    static Properties p=new Properties();
    static {
        try {
            InputStream is=JDBCutils.class.getClassLoader().getResourceAsStream("druid.properties");
            p.load(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static DataSource getDataSource() {
        DataSource ds=null;
        try {
            ds = DruidDataSourceFactory.createDataSource(p);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ds;
    }
    public static Connection getConnection(){
        Connection con=null;
        try {
            con=getDataSource().getConnection();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return con;
    }
}
```

```java
//Userdao.java
package dao;

import doMain.User;
import org.springframework.dao.DataAccessException;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import util.JDBCutils;

public class Userdao {
    private JdbcTemplate tmplate=new JdbcTemplate(JDBCutils.getDataSource());
    public User login(User loginuser){
        try {
            String sql="select * from user where username=? and password=?";
            User user=tmplate.queryForObject(sql,new BeanPropertyRowMapper<User>(User.class),loginuser.getUsername(),loginuser.getPassword());
            return user;
        } catch (DataAccessException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

```java
//user.java
package doMain;

public class User {
    private Integer id;
    private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public String getPassword() {
        return password;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

```java
//LoginServlet.java
package web;

import dao.Userdao;
import doMain.User;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/loginservlet")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        User loginuser=new User();
        loginuser.setUsername(req.getParameter("username"));
        loginuser.setPassword(req.getParameter("password"));
        Userdao dao=new Userdao();
        System.out.println(loginuser);
        User user=dao.login(loginuser);
        if(user==null){
            System.out.println("登陆失败");
        }else {
            System.out.println("登陆成功");
        }
        System.out.println("dopost");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }
}
```

```jsp
<!--index.jsp-->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>Login</title>
  </head>
  <body>
  <form action="/login_war_exploded/loginservlet" method="post">
    <input type="text" name="username" placeholder="username"><br>
    <input type="text" name="password" placeholder="password"><br>
    <input type="submit" value="提交">
  </form>
  </body>
</html>
```

7）BeanUtils

主要提供了对于JavaBean进行各种操作，比如对象,属性复制等等。使用前需导入导入commons-beanutils-1.8.3 包和commons-logging-1.1.3 包

| 注意方法                                                     | 描述                      |
| ------------------------------------------------------------ | ------------------------- |
| public static void setProperty(Object bean, String name, Object value) | 设置指定属性的值          |
| public static String getProperty(Object bean, String name)   | 获取指定属性的值          |
| public static void populate(Object bean, Map<String, ? extends Object> properties) | 将map数据拷贝到javabean中 |

```java
import doMain.User;
import org.apache.commons.beanutils.BeanUtils;
import org.junit.Test;

import java.lang.reflect.InvocationTargetException;
import java.util.HashMap;
import java.util.Map;


public class test {
    @Test
    public static void main(String[] args) throws InvocationTargetException, IllegalAccessException {
        User user=new User();
        Map<String,String> map=new HashMap<>();//Map的第二个泛型可以是数组，执行populate时会取数组的第一个元素
        map.put("username","zhanghuan");
        map.put("password","123456");
        BeanUtils.populate(user,map);
        System.out.println(user);//User{id=null, username='zhanghuan', password='123456'}
    }
}
```

```
/*
JavaBean
1.类必须被public修饰
2.必须提供空参数构造器
3.成员变量必须使用private修饰
4.提供公共的setter和getter方法
*/
```

`6`响应

由响应行、响应头、响应空行、响应体组成

1）响应行格式

协议/版本 响应状态码 状态码解释

2）状态码表示信息

| 状态码 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| 1XX    | 服务器接收客户端消息，但没完成，等待一段时间后，发送1XX状态码 |
| 2XX    | 成功                                                         |
| 3XX    | 重定向，304表示访问缓存，302表示重定向                       |
| 4XX    | 客户端错误，404请求路径没有对应的资源，405请求方式没有对应的doXX方法 |
| 5XX    | 服务器错误，500服务器内部错误                                |

6）响应头

(1)Content-Type

指定数据格式及编码

(2)Content-disposition

指定以什么格式打开响应体，in-line当前页面打开，attachment;filename=xxx以附件形式打开响应体，用于下载

7）响应体

传输的数据

8）获取虚拟目录

```
req.getContextPath();
```

9）重定向

```java
package web;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet("/index")
public class indexServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //方式一
        /*
        resp.setStatus(302);
        resp.setHeader("location",req.getContextPath()+"/login");
         */
        
        //方式二
        resp.sendRedirect(req.getContextPath()+"/login");

    }
}
```

特点：

地址栏里的地址会发生变化，可访问其他站点的资源，发送了两次请求

10）字符输出流

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/login")
public class loginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置writer的字符编码
        resp.setCharacterEncoding("utf-8");

        //给客户端指定字符编码方式一
        //resp.setHeader("Content-Type","text/html;charset=utf-8");

        //给客户端指定字符编码方式二
        resp.setContentType("text/html;charset=utf-8");

        //获取writer
        PrintWriter pr=resp.getWriter();
        //向响应体写入数据
        pr.write("这是登陆页");
    }
}
```

11）验证码案例

```java
import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.*;
import java.util.Random;

public class randomPic {
    static String[] strs=null;

    public static String getRandomString(){
        String str="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
        String result_str="";
        Random rd=new Random();
        for (int i = 0; i <4 ; i++) {
            int num=rd.nextInt(61);
            result_str+=Character.toString(str.charAt(num));
        }
        return result_str;
    }

    public static void drawImg(String str,OutputStream os,int width,int height){
        Random rd=new Random();

        //创建画布
        BufferedImage img=new BufferedImage(width,height,BufferedImage.TYPE_INT_RGB);
        //创建画笔
        Graphics g=img.createGraphics();
        //设置画笔颜色
        g.setColor(Color.PINK);
        //用画笔填充画布
        g.fillRect(0,0,width, height);

        //在画布上写字符串
        g.setColor(Color.BLUE);
        Font f=new Font(null,Font.PLAIN,25);
        g.setFont(f);//设置字体大小
        strs=str.split("");//获取随机字符串
        for (int i = 0; i <strs.length ; i++) {
            g.drawString(strs[i],width/4*i+8,rd.nextInt(height/4)+height/2);
        }

        //在画布上画干扰线
        for (int i = 0; i <10 ; i++) {
            g.drawLine(rd.nextInt(width),rd.nextInt(height),rd.nextInt(width),rd.nextInt(height));
        }

        //将图片写入到流中
        try {
            boolean jpg = ImageIO.write(img, "jpg", os);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

```java
import doMain.randomPic;

import javax.imageio.ImageIO;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet("/login")
public class loginServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String str=randomPic.getRandomString();//获取随机字符串
        System.out.println(str);
        ImageIO.setUseCache(false);//使用内存来存储临时文件，无此句可能报错
        randomPic.drawImg(str,resp.getOutputStream(),100,50);

    }
}
```

12）ServletContex

代表整个web应用，可以和程序容器（服务器）通信

该对象有3个功能：

获取MIME类型，域对象，获取文件正式路径

(1)获取ServletContex对象

```
//1.通过req对象获取
req.getServletContex();
//2.通过HttpServlet对象获取
this.getServletContex();
```

(2)获取MIME类型

MIME类型：互联网通信中定义的一种文件类型，格式为：大类型/小类型

```
String getMimeType(String var1);
```

(3)域对象方法

```
//设置属性
void setAttribute(String var1, Object var2);
//获取属性
Object getAttribute(String var1);
//删除属性
void removeAttribute(String var1);
```

(4)获取正式路径

```
String getRealPath(String var1);
```

注意：这里获取路径的文件默认是web目录下的所有文件，如果要获取的文件是src下的，需要进入WEB-INF/classes目录下获取

13）下载案例

```java
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

@WebServlet("/download")
public class downloadServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext sc=this.getServletContext();
        String contenttype=sc.getMimeType("2.jpg");
        resp.setContentType( contenttype);
        resp.setHeader("content-disposition","attachment;filename=2.jpg");
        //文件名如果是中文需要特殊将中文进行编码

        String path=sc.getRealPath("/imgs/1.jpg");
        InputStream is= new FileInputStream(path);
        ServletOutputStream os=resp.getOutputStream();
        int len;
        byte[] b=new byte[1024];
        while ((len=is.read(b))!=-1){
            os.write(b,0,len);
        }
        os.close();
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }
}
```

`7`cookie

客户端会话技术

```
//创建cookie
Cookie c=new Cookie("name","zhanghuan");
//发送cookie
response.addCookie(c);
//获取cookie
Cookie[] cs=request.getCookies();
```

1）特点：

(1)一次可发送多个cookie

(2)默认情况下，浏览器关闭cookie销毁

(3)可设置cookie的存活时间

```
Cookie c=new Cookie("name","zhanghuan");
c.setMaxAge(30);//设置cookie存活时间为30秒
//setMaxAge的参数为int类型，负数表示删除cookie，0表示浏览器关闭cookie销毁
```

(4)cookie的键值对在tomcat8之后支持中文，但最好还是不要存中文，可对中文进行编码

```
URLEncoder.encode(String s, String enc);//s是需要编码的字符串，enc是编码格式,返回一个字符串
URLDecoder.decode(String s, String enc);//s是需要解码的字符串，enc是解码格式,返回一个字符串
```

2）cookie共享问题

(1)默认情况下，同一服务器下的不同项目不可共享，如果要共享需设置

```
setPath(String path);
//传入path为“/”，表示共享
```

(2)默认情况下，不同服务器下的项目不可共享，如果要共享需设置

```
setDomain(String path);
//与path匹配上的一级域名，可共享该cookie
```

`8`session

服务端会话技术

```
//创建session
HttpSession s=request.getSession();
//设置session
s.setAttribute("name","zhanghuan");
//获取session值
s.getAttribute("name");
//删除session
s.removeAttribute("name");
```

1）存活时间

在服务器存放的session数据能保留30分钟，服务器关闭或session对象调用invalidate方法会销毁session，要修改session在服务器的存活时间，需要到conf目录下的web.xml修改配置

```
<session-config>
	<session-timeout>30</session-timeout>
</session-config>
```

2）服务器或浏览器关闭后对session的影响

(1)浏览器关闭

session依赖于cookie，浏览器关闭后未设置过期时间的cookie会被清除掉，再次打开浏览器发送请求后，服务器读取不到名为JSESSIONID的cookie，便把该次请求当作一个新请求并重新分配session；要解决这个问题，需要给名为JSESSIONID的cookie设置过期时间

(2)服务器关闭

在IDEA中session会发生变化，在tomcat中会自动完成session的钝化、活化操作，session不会发生变化

3）session和cookie的比较

|                  | session | cookie |
| ---------------- | ------- | ------ |
| 位置             | 服务器  | 浏览器 |
| 可存入的数据大小 | 无限制  | 有限制 |
| 安全性           | 安全    | 不安全 |

`9`验证码登陆案例

```java
//获取动态验证码的servlet服务
package web;

import midware.randomPic;

import javax.imageio.ImageIO;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@WebServlet("/getvalidpicServlet")
public class getvalidpicServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String str= randomPic.getRandomString();
        System.out.println(str);
        HttpSession s=request.getSession();
        s.setAttribute("validcode",str);
        ImageIO.setUseCache(false);
        randomPic.drawImg(str,response.getOutputStream(),100,50);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

```jsp
<!--登陆页面-->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
  <form action="<%=request.getContextPath()%>/login">
    <table>
      <tr>
        <td>用户名</td>
        <td><input type="text" name="username"></td>
      </tr>
      <tr>
        <td>密码</td>
        <td><input type="text" name="password"></td>
      </tr>
      <tr>
        <td>验证码</td>
        <td><input type="text" name="validcode"></td>
      </tr>
      <tr>
        <td colspan="2"><img id="vpic" alt=""></td>
      </tr>
      <tr>
        <td colspan="2"><input id="sub" type="submit"></td>
      </tr>
    </table>
  </form>
  </body>
</html>
<script>
  window.onload=function () {
    picsrc()
    document.getElementById("vpic").onclick=function () {
      picsrc()
    }
  }
  function picsrc() {
    var vpic=document.getElementById("vpic")
    var date=new Date()
    vpic.setAttribute("src","<%=request.getContextPath()%>/getvalidpicServlet?a="+date.getSeconds())
  }
</script>
```

```java
//登陆的servl服务
package web;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;


@WebServlet("/login")
public class loginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取传过来的数据
        String username=req.getParameter("username");
        String password=req.getParameter("password");
        String validcode=req.getParameter("validcode");

        //获取session中存放的验证码
        HttpSession s=req.getSession();
        String session_valid=(String)s.getAttribute("validcode");

        //响应
        resp.setCharacterEncoding("utf-8");
        resp.setHeader("Content-Type","text/html;charset=utf-8");
        if(validcode.equalsIgnoreCase(session_valid)){
            if(username.equals("zhanghuan") && password.equals("123")){
                resp.getWriter().write("欢迎你，"+"zhanghuan");
            }else{
                resp.getWriter().write("登陆失败，账号或密码错误");
            }
        }else{
            resp.getWriter().write("登陆失败，验证码错误");
        }
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }
}
```

`10`JSP

java服务端页面，可定义html标签，也可定义java代码，本质是一个servlet

1）指令

用于配置jsp页面，导入资源文件

格式

```
<%@ 指令名 属性名=属性值…… %>
```

指令分类

(1)page

配置jsp页面常用4个属性

| 属性        | 功能                              |
| ----------- | --------------------------------- |
| contentType | 设置页面的MIME属性                |
| import      | 导入java包                        |
| errorPage   | 发生错误时跳转到另一个目标页面    |
| isErrorPage | 标志该页面是否可使用exception对象 |

(2)incluede

页面包含

```
<%@include file="abc.jsp"%>
```

(3)taglib

导入标签库资源

```
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

2）脚本

```
<% 代码 %>//定义内容在service方法中
<%！ 代码 %>//定义内容在jsp转换的java类中的成员位置
<%= 代码 %>//定义内容输出到页面上
```

3）注释

```
<!---->//只能注释html代码
<%---->//能注释所有内容，注释后的内容不会在页面上出现
```

4）内置对象

不需要创建，可直接使用的对象

| 对象名      | 真实类型            | 说明                                |
| ----------- | ------------------- | ----------------------------------- |
| pageContex  | pageContex          | 可获取其他8个对象，当前页面数据共享 |
| request     | HttpServletRequest  | 一次请求数据共享                    |
| session     | HttpSession         | 一次会话数据共享                    |
| application | ServletContext      | 所有用户数据共享                    |
| response    | HttpServletResponse | 响应对象                            |
| page        | Object              | 当前页面对象                        |
| out         | JspWriter           | 输出对象                            |
| config      | ServletConfig       | Servlet配置对象                     |
| exception   | Throwable           | 异常对象                            |

out和response.getWriter().write()都能输出到页面上，但后者输出的内容在前者之前

---

**MVC**

|      | 解释   | 实现元素 | 功能                                           |
| ---- | ------ | -------- | ---------------------------------------------- |
| M    | 模型   | JavaBean | 查询数据库，封装对象                           |
| V    | 视图   | JSP      | 展示数据                                       |
| C    | 控制器 | Servlet  | 获取用户输入，调用模型，将数据交给视图进行展示 |

优点

耦合性低，方便维护，重用性高

缺点

使得项目架构复杂

---

**EL表达式**

Expression Langarage表达式语言，用于替换、简化JSP中的java代码

忽略el表达式

1）设置jsp中page的属性isELIgnored=""设置为true

2）\\${ 表达式 }

`1`运算符

| 类型       | 详细               |
| ---------- | ------------------ |
| 算术运算符 | + -  * / div % mod |
| 比较运算符 | > < ==  >= <= !=   |
| 逻辑运算符 | & and \| or ! not  |
| 定运算符   | empty              |

empty用于判断字符串、集合、数组对象是否为null并且长度为0，是返回为true

```
${ empty list }
${ not empty list }
```

`2`域对象获取值

```
//1.从指定域中获取指定值
${域名称.键名}

//2.依次从最小域中找是否有对应键的值
${键名}
```

`3`获取对象属性值

```
${对象.属性名}
```

`4`隐式对象

el表达式有11个隐式对象，可通过pageContext获取其他8个对象

```
${ pageContext.request.contextPath }
```

---

**JSTL**

JavaServer Pages Tag Library jsp标准标签库，用于替换、简化JSP中的java代码

使用步骤：

1.导入jstl的两个java包

2.JSP中通过指令引入标签库`<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`

3.页面中使用标签

`1`if

相当于java中的if语句

```
<c:if test="true">
  该标签内test属性值为true，会显示出来
</c:if>
<c:if test="false">
  该标签内test属性值为false，不会显示出来
</c:if>
```

`2`choose

相当于java中的switch语句

```
<%
request.setAttribute("num",1);
%>
<c:choose>
  <c:when test="${num==1}">星期一</c:when>
  <c:when test="${num==2}">星期二</c:when>
  <c:when test="${num==3}">星期三</c:when>
  <c:when test="${num==4}">星期四</c:when>
  <c:when test="${num==5}">星期五</c:when>
  <c:when test="${num==6}">星期六</c:when>
  <c:when test="${num==7}">星期七</c:when>
  <c:otherwise>输入错误</c:otherwise>
</c:choose>
```

`3`forEach

相当于java中的for语句

```
<%--1.完成重复性操作--%>
  <table>
    <th> 变量i的值</th>
    <th> 循环次数</th>
    <th> 循环索引</th>
    <c:forEach begin="1" end="5" var="i" step="1" varStatus="s">
      <tr>
        <td> ${i}</td>
        <td> ${s.count}</td>
        <td> ${s.index}</td>
      </tr>
    </c:forEach>
  </table>
  <%--运行结果
    变量i的值	循环次数	循环索引
    1	1	1
    2	2	2
    3	3	3
    4	4	4
    5	5	5
  --%>

<%--2.遍历容器--%>
  <%
  String[] ar={"a","b","c"};
  request.setAttribute("ar",ar);
  %>
<c:forEach items="${ar}" var="str">
  ${str}<br>
</c:forEach>
```

---

**Filter过滤器**

访问服务器资源时，过滤器可以将请求拦截下来，完成同一特殊功能。用于完成一些通用操作，如：登陆验证、统一编码处理、敏感字符过滤等

`1`创建步骤

1）创建类实现Filter接口

2）实现抽象方法，在doFilter里调用`chain.doFilter(req, resp)`，放行请求

3）将该实现类进行注册

```xml
<!--web.xml-->
<filter>
	<filter-name>allFilter</filter-name>
	<filter-class>allFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>allFilter</filter-name>
    <url-pattern>*</url-pattern>
</filter-mapping>
```

```
//另一种方式时在类上面新增注解
@WebFilter(filterName = "/*")
```

`2`生命周期方法

| 方法名   | 解释                                                         |
| -------- | ------------------------------------------------------------ |
| init     | 服务器启动后，会创建Filter对象，然后调用init方法，只执行一次，用于加载资源 |
| doFilter | 每次请求被拦截资源时，会执行，执行多次                       |
| destroy  | 在服务器关闭后，Filter对象被销毁；如果是正常关闭，会执行destroy方法，只执行一次，用于释放资源 |

`3`拦截路径配置

1）具体资源路径

```
@WebFilter("/index.jsp")
```

2）拦截目录

```
@WebFilter("/user/*")
```

3）后缀名拦截

```
@WebFilter("*.jsp")
```

`4`拦截方式配置

1）注解上配置

```
@WebFilter(value="*.jsp",dispatcherTypes=DispatcherType.REQUEST)
```

2）web.xml配置

```xml
<filter>
	<filter-name>allFilter</filter-name>
	<filter-class>allFilter</filter-class>
	<dispatcher>REQUEST</dispatcher>
</filter>
```

3）拦截类型

| 类型                   | 含义           |
| ---------------------- | -------------- |
| DispatcherType.REQUEST | 直接访问时过滤 |
| DispatcherType.FORWARD | 转发访问时过滤 |
| DispatcherType.INCLUDE | 包含时过滤     |
| DispatcherType.ERROR   | 发生错误时过滤 |
| DispatcherType.ASYNC   | 异步时过滤     |

`5`多个过滤器执行的先后顺序

1）注解配置的过滤器

安装类名字符串比较规则比较，值小的先执行

2）web.xml配置

谁定义在上面，谁先执行

`6`过滤器验证登陆案例思路

```
请求过来时，看这个请求访问的资源，如果是登陆相关的就通过，如果不是就判断用户登陆状态；如果用户已登陆，放行通过；为登陆，跳转到登陆界面
```

---

**代理模式**

代理模式给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用。通俗的来讲代理模式就是我们生活中常见的中介

```java
import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

@WebFilter("/*")
public class allFilter implements Filter {
    public void destroy() {
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {

        ServletRequest proxy_req=(ServletRequest)Proxy.newProxyInstance(req.getClass().getClassLoader(), req.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //利用代理对参数进行过滤
                if(method.getName().equals("getParameter")){
                    String value=(String) method.invoke(req,args);
                    value=value.replaceAll("zhanghuan","张欢");
                    System.out.println(value);
                    return value;
                }
                return method.invoke(req,args);
            }
        });
        chain.doFilter(proxy_req,resp);
    }

    public void init(FilterConfig config) throws ServletException {

    }

}
```

---

**监听器**

监听观察某个事件（程序）的发生情况，当被监听的事件真的发生了的时候，事件发生者（事件源） 就会给注册该事件的监听者（监听器）发送消息，告诉监听者某些信息，同时监听者也可以获得一份事件对象，根据这个对象可以获得相关属性和执行相关操作

```java
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;//监听ServletContext的创建和销毁
import javax.servlet.annotation.WebListener;

@WebListener//注册监听器
public class ServletContexListener1 implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {//监听到创建后执行
        System.out.println("ServletContext创建了……");
        
        //获取初始化参数
        String str=sce.getServletContext().getInitParameter("name");
        System.out.println(str);
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {//监听到销毁后执行

    }
}
```

注册监听器也可在web.xml中配置

```xml
<listener>
    <listener-class>com.ygj.control.onLineCount</listener-class>
</listener>
```

初始化参数

在web.xml中配置

```xml
<init-param>
    <param-name>username</param-name>
    <param-value>moonlit</param-value>
</init-param>
```

可通过HttpServlet.getInitParameter(String Param)拿到

---

**Redis**

非关系型数据库，键值对数据，value有5种不同的数据类型，分别是字符串(string)，哈希(hash)，列表(list)，集合(set)，有序集合(sortedset)

`1`操作

| 类型                | 操作 | 命令                 | 说明                   |
| ------------------- | ---- | -------------------- | ---------------------- |
| 字符串(string)      | 存   | set key value        |                        |
|                     | 取   | get key              |                        |
|                     | 删   | del key              |                        |
| 哈希(hash)          | 存   | hset key field value |                        |
|                     | 取   | hget key field       |                        |
|                     |      | hgetall key          | 获取所有的field和value |
|                     | 删   | hdel key field       |                        |
| 列表(list)          | 存   | lpush key value      |                        |
|                     |      | rpush key value      |                        |
|                     | 取   | lrange key start end |                        |
|                     | 删   | lpop key             |                        |
|                     |      | rpop key             |                        |
| 集合(set)           | 存   | sadd key value       |                        |
|                     | 取   | smembers key         |                        |
|                     | 删   | srem key value       |                        |
| 有序集合(sortedset) | 存   | zadd key score value |                        |
|                     | 取   | zrange key start end |                        |
|                     | 删   | zrem key value       |                        |
| 通用                |      | keys *               | 查看所有的键           |
|                     |      | type key             | 查看某一键的数据类型   |
|                     |      | del key              | 删除键                 |

`2`持久化

将内存种的数据保存到硬盘上

(1)RDB

默认方式，无需配置。一定时间间隔中，检测key变化情况，然后持久化数据

```
save 900 1
save 300 10
save 60 10000
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
```

(2)AOF

日志记录方式，可以记录每一条命令的操作，每一次命令操作后，持久化数据

```
# enable the append only mode: when this mode is enabled Redis will append
# every write operation received in the file appendonly.aof.
appendonly no#改为yes

# appendfsync always
appendfsync everysec#每秒持久化一次
# appendfsync no
```

`3`Jedis

通过java操作redis数据库，需导入2个jar包

(1)各类型数据操作

```java
import redis.clients.jedis.Jedis;

import java.util.Map;

public class test01 {
    static Jedis j=new Jedis("localhost",6379);//空参数默认值"localhost",6379
    
    public static void main(String[] args) throws InterruptedException {
        string_value();
        hash_value();
        list_value();
        set_value();
        sortedset_value();
        j.close();//关闭连接

    }
    public static void string_value() throws InterruptedException {
        //设置键值对
        j.set("name","asdsdsd");
        System.out.println(j.get("name"));//asdsdsd

        //删除键值对
        j.del("name");
        System.out.println(j.get("name"));//null

        //设置具有过期时间的键值对
        j.setex("age", 5, "25");
        System.out.println(j.get("age"));//25
        Thread.sleep(10000);
        System.out.println(j.get("age"));//null
        j.close();
    }
    public static void hash_value(){
        //设置
        j.hset("myhashset","name","zhanghuan");
        System.out.println(j.hget("myhashset","name"));//zhanghuan

        //获取map
        Map<String,String> map=j.hgetAll("myhashset");
        System.out.println(map);//{name=zhanghuan, age=25}

        //删除map下的键
        j.hdel("myhashset","name","age");
        System.out.println(j.hgetAll("myhashset"));//{}
    }
    public static void list_value(){
        //压入
        j.lpush("mylist","a");
        j.rpush("mylist","b");
        System.out.println(j.lrange("mylist",0,-1));//[a, b]

        //弹出
        System.out.println(j.lpop("mylist"));//a
        System.out.println(j.rpop("mylist"));//b
    }
    public static void set_value(){
        //存
        j.sadd("myset","大毛");
        j.sadd("myset","小毛");

        //取
        System.out.println(j.smembers("myset"));//[小毛, 大毛]

        //删除指定值
        j.srem("myset","大毛");
        System.out.println(j.smembers("myset"));//[小毛]sortedset
    }
    public static void sortedset_value(){
        //存
        j.zadd("mysortedset",10,"小黄");
        j.zadd("mysortedset",18,"小黑");
        j.zadd("mysortedset",14,"小白");

        //取
        System.out.println(j.zrange("mysortedset",0,-1));//[小黄, 小白, 小黑]

        //删除
        j.zrem("mysortedset","小白");
        System.out.println(j.zrange("mysortedset",0,-1));//[小黄, 小黑]
    }
}

```

(2)封装连接池对象

```
#jedis.properties
host=127.0.0.1
port=6379
MaxTotal=50
MaxIdle=10
```

```java
import org.apache.commons.pool2.impl.GenericObjectPoolConfig;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;


import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class JedisPoolUtils {
    private static JedisPool pool;
    static {
        //读取配置文件
        InputStream is=JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
        Properties p=new Properties();
        try {
            p.load(is);
        } catch (IOException e) {
            e.printStackTrace();
        }

        //创建配置对象
        GenericObjectPoolConfig<String> config=new GenericObjectPoolConfig<>();
        config.setMaxIdle(Integer.parseInt(p.getProperty("MaxIdle")));
        config.setMaxTotal(Integer.parseInt(p.getProperty("MaxTotal")));

        //生成连接池
        pool=new JedisPool(config,p.getProperty("host"),Integer.parseInt(p.getProperty("port")));
    }
    public static Jedis getJedis(){
        return pool.getResource();
    }
}
```

`4`省市二级联动案例

1）数据库

| 类型    | 字段名   | 解释                                                       |
| ------- | -------- | ---------------------------------------------------------- |
| int     | CRI_ID   | id                                                         |
| varchar | CRI_CODE | 6位编码，区分省、市，前3位代表省，第4位代表市，后2位代表取 |
| varchar | CRI_NAME | 省、市、区名称                                             |

2）目录结构

```
-src
	-dao
		-impl
			cn_regionDaoimpl.java
		cn_regionDao.java
	-domain
		Cn_region.java
	-service
		-impl
			getCitiesServiceimpl.java
			getProvincesServiceimpl.java
		getCitiesService.java
		getProvincesService.java
	-utils
		JDBCutils.java
		JEDISPOOLutils.java
	-web
		getCitiesServlet.java
		getProvincesServlet.java
	duid.properties
	jedis.properties
	
web
	-WEB-INF
		-lib
		web.xml
	index.jsp
```

3）文件内容

```java
//cn_regionDaoimpl.java
package dao.impl;

import dao.cn_regionDao;
import doMain.Cn_region;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import utils.JDBCutils;

import java.util.ArrayList;
import java.util.List;

public class cn_regionDaoimpl implements cn_regionDao {
    static private JdbcTemplate template=new JdbcTemplate(JDBCutils.getDataSource());

    @Override
    public List<Cn_region> findAll() {

        List<Cn_region> list=template.query("select CRI_ID,CRI_CODE,CRI_NAME from cn_region_info",new BeanPropertyRowMapper<Cn_region>(Cn_region.class));
        return list;
    }

    @Override
    public List<Cn_region> getProvinces() {
        List<Cn_region> alllist=findAll();
        List<Cn_region> r_list=new ArrayList<Cn_region>();
        String CRI_CODE_last_3;//最后3位，用来判断是否是省
        for (Cn_region item:
                alllist) {
            CRI_CODE_last_3=item.getCRI_CODE().substring(3,6);
            if(CRI_CODE_last_3.equals("000")){
                r_list.add(item);
            }
        }
        return r_list;
    }

    @Override
    public List<Cn_region> getCites(String province_code) {
        List<Cn_region> alllist=findAll();
        List<Cn_region> r_list=new ArrayList<Cn_region>();
        String CRI_CODE_first_3;//截取前三位
        String CRI_CODE_4;//截取第四位
        String CRI_CODE_last_2;//最后两位
        for (Cn_region item:
                alllist) {
            CRI_CODE_first_3=item.getCRI_CODE().substring(0,3);
            CRI_CODE_4=item.getCRI_CODE().substring(3,4);
            CRI_CODE_last_2=item.getCRI_CODE().substring(4,6);
            if( CRI_CODE_first_3.equals (province_code) && !CRI_CODE_4.equals ("0") && CRI_CODE_last_2.equals("00")){
                r_list.add(item);
            }
        }
        return r_list;
    }

    @Override
    public List<Cn_region> getCity_areas() {
        return null;
    }
}

```



```java
//cn_regionDao.java
package dao;

import doMain.Cn_region;

import java.util.List;

public interface cn_regionDao {
    public List<Cn_region> findAll();
    public List<Cn_region> getProvinces();
    public List<Cn_region> getCites(String province_code);
    public List<Cn_region> getCity_areas();
}
```

```java
//Cn_region.java
package doMain;

public class Cn_region {
    private int CRI_ID;
    private String CRI_CODE;
    private String CRI_NAME;

    public int getCRI_ID() {
        return CRI_ID;
    }

    public String getCRI_CODE() {
        return CRI_CODE;
    }

    public String getCRI_NAME() {
        return CRI_NAME;
    }

    public void setCRI_ID(int CRI_ID) {
        this.CRI_ID = CRI_ID;
    }

    public void setCRI_CODE(String CRI_CODE) {
        this.CRI_CODE = CRI_CODE;
    }

    public void setCRI_NAME(String CRI_NAME) {
        this.CRI_NAME = CRI_NAME;
    }

    @Override
    public String toString() {
        return "Cn_region{" +
                "CRI_ID=" + CRI_ID +
                ", CRI_CODE='" + CRI_CODE + '\'' +
                ", CRI_NAME='" + CRI_NAME + '\'' +
                '}';
    }
}

```

```java
//getCitiesServiceimpl.java
package service.impl;

import dao.impl.cn_regionDaoimpl;
import doMain.Cn_region;
import org.codehaus.jackson.map.ObjectMapper;
import redis.clients.jedis.Jedis;
import service.getCitiesService;
import utils.JEDISPOOLutils;

import java.io.IOException;
import java.util.List;

public class getCitiesServiceimpl implements getCitiesService {
    @Override
    public String findAll(String province_code){
        cn_regionDaoimpl dao=new cn_regionDaoimpl();
        List<Cn_region> list=dao.getCites (province_code);
        //序列化
        ObjectMapper maper=new ObjectMapper();
        String json= null;
        try {
            json = maper.writeValueAsString (list);
        } catch (IOException e) {
            e.printStackTrace ( );
        }
        return json;
    }

    @Override
    public String findAllJson(String province_code){
        //先从redis中查找数据，如果没有再从数据库中查找
        Jedis jedis= JEDISPOOLutils.getJedis ();
        String cities= jedis.hget ("cities",province_code);
        String json;
        if(cities==null || cities.length ()==0){
            json=findAll (province_code);
            jedis.hset("cities",province_code,json);
        }else {
            json=cities;
        }
        jedis.close ();
        return json;
    }
}

```

```java
//getProvincesServiceimpl.java
package service.impl;

import dao.impl.cn_regionDaoimpl;
import doMain.Cn_region;
import org.codehaus.jackson.map.ObjectMapper;
import redis.clients.jedis.Jedis;
import service.getProvincesService;
import utils.JEDISPOOLutils;

import java.io.IOException;
import java.util.List;

public class getProvincesServiceimpl implements getProvincesService {
    @Override
    public String findAll() {
        cn_regionDaoimpl dao=new cn_regionDaoimpl();
        List<Cn_region> list=dao.getProvinces ();
        //序列化
        ObjectMapper maper=new ObjectMapper();
        String json= null;
        try {
            json = maper.writeValueAsString (list);
        } catch (IOException e) {
            e.printStackTrace ( );
        }
        return json;
    }

    @Override
    public String findAllJson(){
        //先从redis中查找数据，如果没有再从数据库中查找
        Jedis jedis=JEDISPOOLutils.getJedis ();
        String provinces= jedis.get ("provinces");
        String json;
        if(provinces==null || provinces.length ()==0){
            json=findAll ();
            jedis.set("provinces",json);
        }else {
            json=provinces;
        }
        jedis.close ();
        return json;
    }
}

```

```java
//getCitiesService.java
package service;

import doMain.Cn_region;

import java.io.IOException;
import java.util.List;

public interface getCitiesService {
    public String findAll(String province_code);
    public String findAllJson(String province_code);
}

```

```java
//getProvincesService.java
package service;

import doMain.Cn_region;

import java.io.IOException;
import java.util.List;

public interface getProvincesService {
    public String findAll();
    public String findAllJson();
}

```

```java
//getCitiesServlet.java
package web;

import service.impl.getCitiesServiceimpl;


import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;


@WebServlet("/getCitiesServlet")
public class getCitiesServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws IOException {

        //获取省份id
        String province_id=request.getParameter ("province_id").substring (0,3);

        //获取数据
        getCitiesServiceimpl getCitiesServiceimpl=new getCitiesServiceimpl();
        String json=getCitiesServiceimpl.findAllJson (province_id);

        //返回
        response.setCharacterEncoding ("utf-8");
        response.setHeader ("contentype","application/json;charset=utf-8");
        response.getWriter ().write (json);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        this.doPost (request, response);
    }
}

```

```java
//getProvincesServlet.java
package web;

import service.impl.getProvincesServiceimpl;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

@WebServlet("/getProvincesServlet")
public class getProvincesServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取数据
        getProvincesServiceimpl getProvincesServiceimpl=new getProvincesServiceimpl();
        String json=getProvincesServiceimpl.findAllJson ();

        //返回
        response.setCharacterEncoding ("utf-8");
        response.setHeader ("contentype","application/json;charset=utf-8");
        response.getWriter ().write (json);

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}

```

```jsp
<%--index.jsp--%>
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/5/1
  Time: 下午 06:32
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
  <div class="container">
    <div class="row">
      <div class="col-md-3">
        <div class="panel">
          <div class="panel-body">
            <select class="form-control" id="select1">
            <option value="0">请选则</option>
          </select>
          </div>
        </div>
      </div>
      <div class="col-md-3">
        <div class="panel">
          <div class="panel-body">
            <select class="form-control" id="select2">
              <option value="0">请选则</option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </div>
  </body>
  <script src="http://libs.baidu.com/jquery/2.1.4/jquery.min.js"></script>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
  <script>
    <%--"${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/getProvincesServlet"--%>
    $(function () {
      $.ajax({
        method:"post",
        url:"http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/getProvincesServlet",
        success:function (data) {
          //cri_ID,cri_NAME,cri_CODE
          console.log(data)
          obj=JSON.parse(data)
          for(var i=0;i<obj.length;i++){
            $("#select1").append("<option value="+obj[i]["cri_CODE"]+">"+obj[i]["cri_NAME"]+"</option>")
          }
        }
      })
      $("#select1").change(function () {
        province_id=$(this).val()
        if($(this).val()!="0"){
          $.ajax({
            methd:"get",
            data:{"province_id":province_id},
            url:"http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/getCitiesServlet",
            success:function (data) {
              obj=JSON.parse(data)
              $("#select2").children().remove()
              $("#select2").append('<option value="0">请选则</option>')
              for(var i=0;i<obj.length;i++){
                $("#select2").append("<option value="+obj[i]["cri_CODE"]+">"+obj[i]["cri_NAME"]+"</option>")
              }
            }
          })
        }else{
          $("#select2").children().remove()
          $("#select2").append('<option value="0">请选则</option>')
        }
      })
    })
  </script>
</html>

```

---

**Maven**

为解决项目开发过程中，导入jar包冲突问题。通过导入jar包的坐标，编译时从仓库中找到对应的包完成编译

`1`安装及环境配置

1）解压安装包

2）配置环境变量

MAVEN_HOME指向解压后的安装包位置

> 注意：
>
> maven运行需要java环境，确保环境变量中有JAVA_HOME

`2`仓库

分为本地仓库、远程仓库、中央仓库。maven会先在本地仓库找jar包，如未找到再去远程仓库找。

要为maven指定本地仓库，需要在conf/setting.xml中配置

```
<localRepository>C:\Users\admin\Desktop\maven_repository</localRepository>
```

路径中不要有中文

3）配置镜像

修改目录下conf下的setting.xml在mirrors标签中，添加文末这一段

```
<mirror>
<id>nexus-aliyun</id>
<mirrorOf>central</mirrorOf>
<name>Nexus aliyun</name>
<url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```

`3`maven项目标准目录结构

```
src/main/java	//核心代码部分
src/main/resources	//配置文件部分
src/test/java	//测试代码部分
src/test/resources	//测试配置文件部分
src/main/webapp	//静态资源，如js,css,图片等
```

`4`命令

| 命令        | 功能                                      |
| ----------- | ----------------------------------------- |
| mvn clean   | 删除编译信息                              |
| mvn compile | 编译项目                                  |
| mvn test    | 编译测试代码                              |
| mvn package | 项目编译，并打成war包                     |
| man install | 项目编译，并打成war包，将项目复制到本地库 |

`5`maven生命周期

| 生命周期     | 操作             | 命令    |
| ------------ | ---------------- | ------- |
| 清理生命周期 | 清除项目编译信息 | clean   |
| 默认生命周期 | 编译             | compile |
|              | 测试             | test    |
|              | 打包             | package |
|              | 安装             | install |
|              | 发布             | deploy  |
| 站点生命周期 |                  |         |

`6`项目对象模型pom和依赖管理模型dependency

1）项目对象模型pom

包含项目自身信息，项目运行依赖的jar包，项目运行环境信息(JDK,tomcat信息)

2）依赖管理模型dependency

包含公司组织名称、项目名、版本号

```
<dependency>
			<!-- junit的项目名称 -->
			<groupId>junit</groupId>
			<!-- junit的模块名称 -->
			<artifactId>junit</artifactId>
			<!-- junit版本 -->
			<version>4.9</version>
			<!-- 依赖范围：单元测试时使用junit -->
			<scope>test</scope>
</dependency>

```

`7`idea下使用maven

1）配置集成环境

在settins搜索maven，将maven的解压目录配置上即可

2）创建maven项目

(1)新建项目，选中maven，点击下一步，填写项目信息，在点击下一步至finish

(2)创建项目后，里面缺少的标准目录需要自行添加，并标记为标准目录

(3)webapp的目录，需要进行静态标记。project structure->modules->web->web resource dirictory中加入包的目录

> 注意
>
> 创建web项目，勾选create from archetype后，选中org.apache.maven.archetpes.maven-archetype-webapp

3）项目中导入jar包

在pom.xml中配置

```
 <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
    </dependency>
  </dependencies>
```

> 注意
>
> 如果导入jar包后，项目中仍无法识别导入的包，可能与maven版本有关系

4）设置包的作用范围

```
 <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <!--导入的servlet和jsp的包要配置范围provided-->
```

这里的scope里包含的就是包的作用范围

| 依赖范围 | 编译 | 测试 | 运行 | 案例                  |
| -------- | ---- | ---- | ---- | --------------------- |
| compile  | 影响 | 影响 | 影响 | spring-core           |
| test     |      | 影响 |      | junit                 |
| provided | 影响 | 影响 |      | servlet-api           |
| runtime  |      | 影响 | 影响 | jdbc驱动              |
| system   | 影响 | 影响 |      | 本地的maven库之外的库 |

5）配置运行环境

```
 <build>
      <plugins>
          <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.2</version>
            <configuration>
              <path>/</path> <!--项目访问路径。当前配置的访问是localhost:9090/, 如果配置是/aa，则访问路径为localhost:9090/aa -->
              <port>9090</port>
              <uriEncoding>UTF-8</uriEncoding><!-- 非必需项 -->
            </configuration>
          </plugin>
      </plugins>
  </build>
```

运行时指向tomcat7:run，不然会报错




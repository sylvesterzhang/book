---
layout: post
title: Mybatis
tags: java
categories: backends
---
框架和架构、MyBatis概述、入门、增删改查操作、dao实现类增删改查、引用外部配置文件、配置实体类别名、注册指定包内的dao接口、动态sql、一对多查询、多对多查询、延迟加载、mybatis缓存、注解开发

**框架和架构**

`1`框架

软件开发的一套解决方案，不同框架解决不同问题。框架中封装很多细节，开发者使用极简方式完成，大大提高效率

`2`三层架构

表现层：用于展示数据

业务层：用于处理业务需求

持久层：和数据库交互

`3`持久层技术解决方案

|        | 技术方案             | 详细               |
| ------ | -------------------- | ------------------ |
| 规范   | JDBC                 | Connection         |
|        |                      | PreparedStatement  |
|        |                      | ResultSet          |
| 工具类 | Spring的JdbcTemplate | Spring对Jdbc的封装 |
|        | Apache的DButils      | Apache对Jdbc的封装 |

---

**MyBatis概述**

`1`概述

一个用java编写的持久层框架，封装了jdbc操作的很多细节，使开发者只需关注sql语句本身，无需关注注册驱动、创建连接等复杂过程，使用ORM思想实现对结果集的封装

`2`ORM

Object Relational Mapping对象关系映射，把数据库里的表和实体类及其属性关联对应起来，通过操作实体类实现对数据库表的操作

---

**入门**

`1`环境搭建

1）创建maven工程，并导入相关依赖坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.zhanghuan</groupId>
    <artifactId>mybatis1</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.4</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
        </dependency>
    </dependencies>

</project>
```

2）创建Mybatis的主配置文件SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test1?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username"  value="root"/>
                <property name="password"  value=""/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="dao/IStudentdao.xml"/>
    </mappers>
</configuration>
```

3）创建实体类和dao接口

```java
package doMain;

public class Student {
    private Integer id;
    private String male;
    private  String name;
    private Integer math;
    private Integer english;

    public Integer getId() {
        return id;
    }

    public String getMale() {
        return male;
    }

    public String getName() {
        return name;
    }

    public Integer getMath() {
        return math;
    }

    public Integer getEnglish() {
        return english;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public void setMale(String male) {
        this.male = male;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setMath(Integer math) {
        this.math = math;
    }

    public void setEnglish(Integer english) {
        this.english = english;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", male='" + male + '\'' +
                ", name='" + name + '\'' +
                ", math=" + math +
                ", english=" + english +
                '}';
    }
}
```

```java
package dao;

import doMain.Student;

import java.util.List;

public interface IStudentdao {
    List<Student> findAll();
}
```

4）创建与dao接口对应的mapper配置文件（注解配置mapper可省略此步）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.IStudentdao">
    <select id="findAll" resultType="doMain.Student">
        select * from student
    </select>
</mapper>
```

`2`使用

```java
import dao.IStudentdao;
import doMain.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class mybatisTest {
    public static void main(String[] args) throws IOException {
        //读取配置文件
        InputStream in= Resources.getResourceAsStream ("SqlMapConfig.xml");

        //创建sessionFactory工厂
        SqlSessionFactoryBuilder builder=new SqlSessionFactoryBuilder ();
        SqlSessionFactory factory=builder.build (in);

        //创建SqlSession对象
        SqlSession session=factory.openSession ();//可传参，true表示关闭事务

        //通过SqlSession对象创建对应接口的代理对象
        IStudentdao studentdao=session.getMapper (IStudentdao.class);

        //使用代理对象的方法
        List<Student> students=studentdao.findAll ();
        for (Student student:
             students) {
            System.out.println (student);
        }

        //释放资源
        session.close ();
        in.close ();
    }
}
```

`3`设计模式

| 操作           | 设计模式   | 详细                                                     |
| -------------- | ---------- | -------------------------------------------------------- |
| 创建工厂       | 构建者模式 | 把对象的创建细节隐藏，使用者直接调用方法便可拿到工厂对象 |
| 生成SqlSession | 工厂模式   | 解耦，降低类之间的依赖关系                               |
| 创建dao实例    | 代理模式   | 不修改源代码的基础上对已有方法进行增强                   |

`4`注解方式配置mapper

1）无需创建与dao接口对应的mapper配置文件，在dao接口的方法上使用注解(@select,@update,@delete,@insert)并传入sql语句

2）在主配置文件中配置mapper，使用class属性指向dao接口的全类名

---

**增删改查操作**

`1`定义dao接口

```java
package dao;

import doMain.Student;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface IStudentdao {
    List<Student> findAll();
    void addStudent(Student st);//插入一条数据
    void updateStudent(Student st);//更新一条记录
    void deleteStudent(int id);//删除一条记录
    Student findOneStudent(int id);//查找一条数据
    List<Student> findFuzzyByName(String name);//模糊查询
    int findTotal();//查询总的记录数
}
```

`2`dao接口的xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.IStudentdao">
    <select id="findAll" resultType="doMain.Student">
        select * from student
    </select>
    <insert id="addStudent" parameterType="doMain.Student">
        <selectKey keyProperty="id" keyColumn="id" order="BEFORE" resultType="Integer">
        <!--在数据插入前，该标签计算出插入之后的id并将其赋值给Student类的id属性-->
            select last_insert_id()
        </selectKey>
        insert into student (id ,male,name,math,english) value (#{id},#{male},#{name},#{math},#{english})
    </insert>
    <update id="updateStudent" parameterType="doMain.Student">
        update student set name=#{name},male=#{male},math=#{math},english=#{english} where id=#{id}
    </update>
    <delete id="deleteStudent" parameterType="java.lang.Integer">
        delete from student where id=#{id}
    </delete>
    <select id="findOneStudent" parameterType="java.lang.Integer" resultType="doMain.Student">
        select * from student where id=#{id}
    </select>
    <select id="findFuzzyByName" parameterType="java.lang.String" resultType="doMain.Student">
        select * from student where name like '%${vale}%'
    </select>
    <select id="findTotal" resultType="int">
        select count(*) from student
    </select>
</mapper>
```

`3`定义测试类

```java
import dao.IStudentdao;
import doMain.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class mybatisTest {
    static InputStream in= null;
    static SqlSession session=null;

    @Before
    public void init(){
        //读取配置文件
        try {
            in = Resources.getResourceAsStream ("SqlMapConfig.xml");
        } catch (IOException e) {
            e.printStackTrace ( );
        }

        //创建sessionFactory工厂
        SqlSessionFactoryBuilder builder=new SqlSessionFactoryBuilder ();
        SqlSessionFactory factory=builder.build (in);

        //创建SqlSession对象
        session=factory.openSession ();
    }
    @After
    public void destroy(){
        //释放资源
        try {
            session.close ();
            in.close ();
        } catch (IOException e) {
            e.printStackTrace ( );
        }
    }

    //查看所有数据
    @Test
    public void findall(){
        //通过SqlSession对象创建对应接口的代理对象
        IStudentdao studentdao=session.getMapper (IStudentdao.class);

        //使用代理对象的方法
        List<Student> students=studentdao.findAll ();
        for (Student student:
                students) {
            System.out.println (student);
        }
    }

    //插入一条记录
    @Test
    public void addStudent(){
        Student st=new Student ();
        st.setId (9);
        st.setName ("zhanghuan");
        st.setMale ("男");
        st.setEnglish (66);
        st.setMath (89);
        IStudentdao studentdao=session.getMapper (IStudentdao.class);
        studentdao.addStudent (st);//调用代理对象的方法
        session.commit ();
    }

    //更新一条记录
    @Test
    public void updateStudent(){
        Student st=new Student ();
        st.setId (9);
        st.setName ("张欢");
        st.setMale ("男");
        st.setEnglish (66);
        st.setMath (89);
        IStudentdao studentdao=session.getMapper (IStudentdao.class);
        studentdao.updateStudent (st);//调用代理对象的方法
        session.commit ();
    }

    //删除一条记录
    @Test
    public void deleteStudent(){
        IStudentdao studentdao=session.getMapper (IStudentdao.class);
        studentdao.deleteStudent (11);//调用代理对象的方法
        session.commit ();
    }

    //查找一条数据
    @Test
    public void findOneStudent(){
        IStudentdao studentdao=session.getMapper (IStudentdao.class);
        Student st=studentdao.findOneStudent (9);//调用代理对象的方法
        System.out.println (st);
    }

    //模糊查询
    @Test
    public void findFuzzyByName(){
        IStudentdao studentdao=session.getMapper (IStudentdao.class);
        List<Student> list=studentdao.findFuzzyByName ("张");//调用代理对象的方法
        for (Student student:
                list) {
            System.out.println (student);
        }
    }

    //查询总的记录数
    @Test
    public void finTotal(){
        IStudentdao studentdao=session.getMapper (IStudentdao.class);
        int total=studentdao.findTotal();//调用代理对象的方法
        System.out.println (total);
    }
}
```

`4`类属性和表列名不对应情况

默认情况下，类的属性需要和表名对应，否则数据无法封装到类里。如果不想对应，可以将类的属性和表的列名进行对应，对应操作在Mapper配置文件中进行

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.IStudentdao">
	<resultMap id="studentmap" type="doMain.Student">
        <id property="id1" column="id"></id>
        <result property="male1" column="male"></result>
        <result property="name1" column="name"></result>
        <result property="math1" column="math"></result>
        <result property="english1" column="english"></result>
    </resultMap>
    <select id="findAll" resultMap="studentmap">
        select * from student
    </select>
</mapper>
```

---

**dao实现类增删改查**

```java
package dao.daoimpl;

import org.apache.ibatis.session.SqlSessionFactory;
import dao.IStudentdao;
import doMain.Student;
import org.apache.ibatis.session.SqlSession;

import java.util.List;

public class IStudentdaoimpl implements IStudentdao {
    private SqlSessionFactory factory;
    public IStudentdaoimpl(SqlSessionFactory factory) {
        this.factory=factory;
    }

    public List<Student> findAll() {
        SqlSession session=this.factory.openSession ();
        List<Student> list=session.selectList ("dao.IStudentdao.findAll");
        session.close ();
        return list;
    }

    public void addStudent(Student st) {
        SqlSession session=this.factory.openSession ();
        session.update ("dao.IStudentdao.addStudent",st);
        session.commit ();
        session.close ();
    }

    public void updateStudent(Student st) {
        SqlSession session=this.factory.openSession ();
        session.update ("dao.IStudentdao.updateStudent",st);
        session.commit ();
        session.close ();
    }

    public void deleteStudent(int id) {
        SqlSession session=this.factory.openSession ();
        session.delete ("dao.IStudentdao.deleteStudent",id);
        session.commit ();
        session.close ();
    }

    public Student findOneStudent(int id) {
        SqlSession session=this.factory.openSession ();
        Student s=session.selectOne ("dao.IStudentdao.findOneStudent",id);
        session.close ();
        return s;
    }

    public List<Student> findFuzzyByName(String name) {
        SqlSession session=this.factory.openSession ();
        List<Student> list=session.selectList ("dao.IStudentdao.findFuzzyByName",name);
        session.close ();
        return list;
    }

    public int findTotal() {
        SqlSession session=this.factory.openSession ();
        int count=session.selectOne ("dao.IStudentdao.findTotal");
        session.close ();
        return count;
    }
}

```

```java
import dao.IStudentdao;
import dao.daoimpl.IStudentdaoimpl;
import doMain.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class mybatisTest1 {
    static InputStream in= null;
    static SqlSessionFactory factory=null;
    static IStudentdao dao=null;
    @Before
    public void init(){
        //读取配置文件
        try {
            in = Resources.getResourceAsStream ("SqlMapConfig.xml");
        } catch (IOException e) {
            e.printStackTrace ( );
        }

        //创建sessionFactory工厂
        SqlSessionFactoryBuilder builder=new SqlSessionFactoryBuilder ();
        SqlSessionFactory factory=builder.build (in);

        dao=new IStudentdaoimpl (factory);
    }
    @After
    public void destroy(){
        //释放资源
        try {
            in.close ();
        } catch (IOException e) {
            e.printStackTrace ( );
        }
    }

    //查看所有数据
    @Test
    public void findall(){
        List<Student> list=dao.findAll ();
        for (Student s:
             list) {
            System.out.println (s);
        }
    }

    //插入一条记录
    @Test
    public void addStudent(){
        Student st=new Student ();
        st.setId (9);
        st.setName ("zhanghuan");
        st.setMale ("男");
        st.setEnglish (66);
        st.setMath (89);
        dao.addStudent (st);
    }

    //更新一条记录
    @Test
    public void updateStudent(){
        Student st=new Student ();
        st.setId (14);
        st.setName ("张欢");
        st.setMale ("男");
        st.setEnglish (66);
        st.setMath (89);
        dao.updateStudent (st);
    }

    //删除一条记录
    @Test
    public void deleteStudent(){
        dao.deleteStudent (15);
    }

    //查找一条数据
    @Test
    public void findOneStudent(){
        Student s=dao.findOneStudent (14);
        System.out.println (s);
    }

    //模糊查询
    @Test
    public void findFuzzyByName(){
        List<Student> list=dao.findFuzzyByName ("张");
        for (Student s:
                list) {
            System.out.println (s);
        }
    }

    //查询总的记录数
    @Test
    public void finTotal(){
        int count=dao.findTotal ();
        System.out.println (count);
    }
}
```

---

**引用外部配置文件**

`1`基本使用

1）外部定义一个以properties结尾的文件

```
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/test1?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
username=root
password=
```

2）导入配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="db.properties"></properties><!--导入-->
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED"><!--以下取出属性值-->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username"  value="${username}"/>
                <property name="password"  value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="dao/IStudentdao.xml"/>
    </mappers>
</configuration>
```

`2`resource属性和url属性

1）resource属性

用于指定配置文件的位置，其值按照类路径的写法来写，并文件必须存在于类路径下

2）url属性

Unifor Resource Locator统一资源定位符，可唯一标识一个资源位置。

格式：协议 主机 端口 URI

URI

Uniform Resource Identifier统一资源标识符，可在应用中唯一定位一个资源

---

**配置实体类别名**

`1`配置全限定名和别名

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="db.properties"></properties>
    <typeAliases>
        <typeAlias type="doMain.Student" alias="student"></typeAlias>
        <!--type里放全限定名，alias放别名，配置后在mapper的xml文件中使用类用别名即可，不区分大小写-->
    </typeAliases>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username"  value="${username}"/>
                <property name="password"  value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="dao/IStudentdao.xml"/>
    </mappers>
</configuration>
```

`2`指定包名

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="db.properties"></properties>
    <typeAliases>
        <package name="doMain"></package>
        <!--name放的是包名，这样配置后包里的类名就是别名，使用时不区分大小写-->
    </typeAliases>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username"  value="${username}"/>
                <property name="password"  value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="dao/IStudentdao.xml"/>
    </mappers>
</configuration>
```

---

**注册指定包内的dao接口**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="db.properties"></properties>
    <typeAliases>
        <package name="doMain"></package>
    </typeAliases>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username"  value="${username}"/>
                <property name="password"  value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <package name="dao"></package>
        <!--这样配置后，dao包下所有接口将会被注册，这里无需写mapper标签进行注册-->
    </mappers>
</configuration>
```

---

**动态sql**

`1`if标签

```xml
<select id="findStudentByCondition" parameterType="Student" resultType="Student">
        select * from student where 1=1
	<if test="name!=null">
            and name=#{name}
	</if>
</select>
```

`2`where标签

```xml
<select id="findStudentByCondition" parameterType="Student" resultType="Student">
	select * from student
	<if test="name!=null">
		<where>
			name=#{name}
		</where>
	</if>
</select>
```

`3`foreach标签

```xml
<select id="findStudentByCondition" parameterType="vo" resultType="Student">
	select * from student
        <where>
            <if test="ids!=null and ids.length>0">
                <foreach collection="ids" open="and id in (" close=")" item="id" separator="," >
                    #{id}
                </foreach>
            </if>
        </where>
</select>
```

`4`sql标签

该标签抽取重复的sql语句

```xml
<sql id="default">select * from student</sql>
<select id="findAll" resultType="doMain.Student">
	<include refid="default"></include>
</select>
```

---

**一对多查询**

`1`表分析

1）user表

| id   | username     | password |
| ---- | ------------ | -------- |
| 1    | zhanghuan    | 123456   |
| 2    | wangdong     | 123456   |
| 3    | liuqiangdong | 123456   |
| 4    | mayun        | 123456   |
| 5    | wangjianling | 123456   |

2）account表

| id   | uid  | money     |
| ---- | ---- | --------- |
| 1    | 1    | 500       |
| 2    | 1    | 1000      |
| 3    | 2    | 545584564 |
| 4    | 1    | 845987    |
| 5    | 4    | 323231232 |
| 6    | 5    | 12555553  |

account表通过uid与user表关联，需要通过account表查询到对应的user信息，及通过user表查到对应的account信息

`2`account表查询到对应的user代码

```java
package doMain;

public class AccountUser {
    private int id;
    private int uid;
    private int money;
    private User user;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int getUid() {
        return uid;
    }

    public void setUid(int uid) {
        this.uid = uid;
    }

    public int getMoney() {
        return money;
    }

    public void setMoney(int money) {
        this.money = money;
    }

    @Override
    public String toString() {
        return "AccountUser{" +
                "id=" + id +
                ", uid=" + uid +
                ", money=" + money +
                ", user=" + user +
                '}';
    }
}

```

```java
package dao;

import doMain.AccountUser;

import java.util.List;

public interface AccountDao {
    List<AccountUser> findAll_account_user();
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.AccountDao">
    <resultMap id="useraccount" type="AccountUser">
        <id property="id" column="id"></id>
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
        <association property="user" column="uid">
            <id property="id" column="uid"></id>
            <result property="username" column="username"></result>
            <result property="password" column="password"></result>
        </association>
    </resultMap>
    <select id="findAll_account_user" resultMap="useraccount">
        select * from account as a left join user as b on  a.uid=b.id
    </select>
</mapper>
```

```java
//省略部分代码
	@Test
    public void account_user_findall(){
        AccountDao dao=session.getMapper (AccountDao.class);
        List<AccountUser> list=dao.findAll_account_user ();
        for (AccountUser au:
                list) {
            System.out.println (au);
        }
    }
```

`3`通过user表查到对应的account代码

```java
package doMain;

import java.util.List;

public class UserAccount {
    private int id;
    private String username;
    private String password;
    private List<Account> accounts;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public List<Account> getAccounts() {
        return accounts;
    }

    public void setAccounts(List<Account> accounts) {
        this.accounts = accounts;
    }

    @Override
    public String toString() {
        return "UserAccount{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", accounts=" + accounts +
                '}';
    }
}

```

```java
package dao;

import doMain.UserAccount;

import java.util.List;

public interface UserDao {
    List<UserAccount> fiidAll_UserAccount();
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="dao.UserDao">
    <resultMap type="UserAccount" id="useraccount">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <collection property="accounts" ofType="Account">
            <id property="id" column="aid"></id>
            <result property="uid" column="id"></result>
            <result property="money" column="money"></result>
        </collection>
    </resultMap>
    <select id="fiidAll_UserAccount" resultMap="useraccount" >
        select u.*,a.id as aid,a.money from user as u left join account as a on u.id=a.uid
    </select>
</mapper>
```

```java
//省略部分代码
	@Test
    public void user_account_findall(){
        UserDao dao=session.getMapper (UserDao.class);
        List<UserAccount> list=dao.fiidAll_UserAccount ();
        for (UserAccount ua:
                list) {
            System.out.println (ua);
        }
    }
```

---

**多对多查询**

`1`表分析

1）user表

| id   | username     | password |
| ---- | ------------ | -------- |
| 1    | zhanghuan    | 123456   |
| 2    | wangdong     | 123456   |
| 3    | liuqiangdong | 123456   |
| 4    | mayun        | 123456   |
| 5    | wangjianling | 123456   |

2）role表

| id   | role | describe                     |
| ---- | ---- | ---------------------------- |
| 1    | 员工 | 公司职员，需完成工作         |
| 2    | 老板 | 公司负责人                   |
| 3    | 学生 | 学校内的学院，主要任务是学习 |
| 4    | 老师 | 学校内的职工，工作内容是讲课 |

3）user_m_role表（关联表）

| id   | uid  | rid  |
| ---- | ---- | ---- |
| 1    | 1    | 1    |
| 2    | 1    | 3    |
| 3    | 4    | 2    |
| 4    | 4    | 4    |

user表与role表通过第三张表user_m_role关联，从user表开始先与user_m_role表进行左联，其查询结果再与role表进行左联，得出user表及其对应的role角色

`2`代码

```java
package doMain;

import java.util.List;

public class User_m_Role {
    private int id;
    private String username;
    private String password;
    private List<Role> roles;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public List<Role> getRoles() {
        return roles;
    }

    public void setRoles(List<Role> roles) {
        this.roles = roles;
    }

    @Override
    public String toString() {
        return "User_m_Role{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", roles=" + roles +
                '}';
    }
}

```

```java
package dao;

import doMain.User;
import doMain.User_m_Role;

import java.util.List;

public interface UserDao {
    List<User_m_Role> find_User_m_Role();
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="dao.UserDao">
    <resultMap type="user_m_role" id="user_m_role">
        <id property="id" column="uid"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <collection property="roles" ofType="Role">
            <id property="id" column="rid"></id>
            <result property="role" column="role"></result>
            <result property="describe" column="describe"></result>
        </collection>
    </resultMap>
    <select id="find_User_m_Role" resultMap="user_m_role">
        SELECT a.id AS uid,a.username,a.`password`,c.id AS rid,c.role,c.`describe`
        FROM user AS a LEFT JOIN user_m_role As b
        ON a.id=b.uid
        LEFT JOIN role AS c
        ON  b.rid=c.id
    </select>
</mapper>
```

> 注意
>
> 1.resultMap标签内，id，collection,association必写，其他属性可以省略，mybatis会自动补充上，association用到的列必须设置result标签，否则mybatis不会将数据封装到对应实体类的属性上
>
> 2.collection和association必须防止在result下，否则会报错

```java
//省略部分代码
	@Test
    public void user_m_role(){
        UserDao dao=session.getMapper (UserDao.class);
        List<User_m_Role> list=dao.find_User_m_Role ();
        for (User_m_Role ur:
                list) {
            System.out.println (ur);
        }
    }
```

---

**延迟加载**

真正使用数据时发起查询，不使用不查询，用于一对多和多对多关系表中

1）主配置文件中进行配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings><!--在这里配置-->
        <setting name="lazyLoadingEnabled" value="true"></setting>
        <setting name="aggressiveLazyLoading" value="false"></setting>
    </settings>
    <typeAliases>
        <package name="doMain"></package>
    </typeAliases>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test1?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username"  value="root"/>
                <property name="password"  value=""/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <package name="dao"></package>
    </mappers>
</configuration>
```

2）mapper配置文件

```xml
<!--一对多-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.UserDao">
    <resultMap id="default" type="UserAccount">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <collection property="accounts" column="id" select="dao.AccountDao.findByUid"></collection>
        <!--column的内容将作为参数传入select内dao.AccountDao.findByUid进行查找-->
    </resultMap>
    <select id="findAll" resultMap="default">
        SELECT * FROM user
    </select>
    <select id="findById" parameterType="int" resultType="User">
        SELECT * FROM user WHERE id=#{id}
    </select>
</mapper>
```

```xml
<!--多对一-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.AccountDao">
    <resultMap id="default" type="AccountUser">
        <id property="id" column="id"></id>
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
        <association property="user" column="uid" javaType="User" select="dao.UserDao.findById"></association>
    </resultMap>
    <select id="findAll" resultMap="default">
        SELECT * FROM account
    </select>
    <select id="findByUid" parameterType="int" resultType="Account">
        SELECT  * FROM account WHERE uid=#{uid}
    </select>
</mapper>
```

---

**mybatis缓存**

经常查询且不经常改变，数据的正确与否对最终结果影响不大，可使用缓存

1）一级缓存

在SqlSession层面，缓存内容是SqlSession对象，通过SqlSession.clearCache()或SqlSession.close()清除掉缓存

2）二级缓存

在namespace层面，缓存内容是SqlSession查询到的数据

(1)主配置文件中

```xml
<settings>
	<setting name="cacheEnabled" value="true"></setting>
</settings>
```

(2)mapper配置文件中

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.UserDao">
    <cache></cache><!--创建cache-->
    <resultMap id="default" type="UserAccount">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <collection property="accounts" column="id" select="dao.AccountDao.findByUid"></collection>
    </resultMap>
    <select id="findAll" resultMap="default" useCache="true"><!--使用cache-->
        SELECT * FROM user
    </select>
    <select id="findById" parameterType="int" resultType="User">
        SELECT * FROM user WHERE id=#{id}
    </select>
</mapper>
```

---

**注解开发**

除xml配置mapper外，还可通过注解方式配置。二者只可选其一，否则会报错，使用注解方式，resource目录中对应的xml配置文件应删除。

`1`快速开始

1）主配置文件中configuration下

```xml
<mappers>
	<!--<mapper class="dao.StudentDao"/>-->
	<package name="dao"></package>
</mappers>
```

2）dao接口

```java
package dao;

import doMain.Student;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface StudentDao {
    @Select ("select * from student")
    @Results(id="all",value = {//对象属性和查询结果进行映射,可不配置映射但要求property和column对应
            @Result(id=true,property = "id",column = "id"),
            @Result(property = "male",column = "male"),
            @Result(property = "name",column = "name"),
            @Result(property = "math",column = "math"),
            @Result(property = "english",column = "english"),
    })
    List<Student> findAll();
}
```

`2`增删改查

```java
package dao;

import doMain.Student;
import org.apache.ibatis.annotations.*;

import java.util.List;

public interface StudentDao {
    @Select ("select * from student")
    @Results(id="all",value = {//对象属性和查询结果进行映射,可不配置映射但要求property和column对应
            @Result(id=true,property = "id",column = "id"),
            @Result(property = "male",column = "male"),
            @Result(property = "name",column = "name"),
            @Result(property = "math",column = "math"),
            @Result(property = "english",column = "english"),
    })
    List<Student> findAll();

    @Insert ("insert into student (id,male,name,math,english) values (#{id},#{male},#{name},#{math},#{english})")
    @SelectKey(statement = "select last_insert_id()", keyProperty = "id", before = true, resultType = int.class)
    void insertOne(Student st);

    @Update ("update student set male=#{male},name=#{name},math=#{math},english=#{english} where id=#{id}")
    void updateOne(Student st);

    @Delete ("delete from student where id=#{id}")
    void deleteOne(int id);

    @Select ("select * from student where id=#{id}")
    Student findById(int id);

    @Select ("select * from student where name like '%${name}%'")
    List<Student> findByNamne(String name);

    @Select ("select count(*) from student ")
    int Count();
}
```

```java
import dao.StudentDao;
import doMain.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class mybatisAnnotationTest {
    SqlSessionFactory factory=null;
    SqlSession session=null;
    @Before
    public void init() throws IOException {
        InputStream in= Resources.getResourceAsStream ("sqlMapConfig.xml");
        factory=new SqlSessionFactoryBuilder().build (in);
        session=factory.openSession ();
        in.close ();
    }
    @After
    public void destroy(){
        session.commit ();
        session.close ();
    }
    @Test
    public void studentFindAll(){
        StudentDao dao=session.getMapper (StudentDao.class);
        List<Student> students=dao.findAll ();
        for (Student s:
             students) {
            System.out.println (s);
        }
    }
    @Test
    public void studentinsertOne(){
        Student student=new Student ();
        student.setName ("周迅");
        student.setMale ("女");
        student.setMath (66);
        student.setEnglish (72);
        StudentDao dao=session.getMapper (StudentDao.class);
        dao.insertOne (student);
    }
    @Test
    public void studentupdateOne(){
        Student student=new Student ();
        student.setId (15);
        student.setName ("周迅讯");
        student.setMale ("女");
        student.setMath (66);
        student.setEnglish (72);
        StudentDao dao=session.getMapper (StudentDao.class);
        dao.updateOne (student);
    }
    @Test
    public void studentdeleteOne(){
        StudentDao dao=session.getMapper (StudentDao.class);
        dao.deleteOne (10);
    }
    @Test
    public void studentfindById(){
        StudentDao dao=session.getMapper (StudentDao.class);
        Student student=dao.findById (15);
        System.out.println (student);
    }
    @Test
    public void studentfindByNamne(){
        StudentDao dao=session.getMapper (StudentDao.class);
        List<Student> students=dao.findByNamne ("张");
        for (Student s:
             students) {
            System.out.println (s);
        }
    }
    @Test
    public void studentCount(){
        StudentDao dao=session.getMapper (StudentDao.class);
        int count=dao.Count ();
        System.out.println (count);
    }
}

```

`3`一对一和一对多关系表

```java
package dao;

import doMain.Account;
import doMain.AccountUser;
import org.apache.ibatis.annotations.One;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.mapping.FetchType;

import java.util.List;

public interface AccountDao {
    @Select ("select * from account")
    @Results(id="default" ,value = {
            @Result(id=true,property = "id",column = "id"),
            @Result(property = "uid",column = "uid"),
            @Result(property = "money",column = "money"),
            @Result(property = "user",column = "uid",one=@One(select = "dao.UserDao.findById",fetchType = FetchType.EAGER)),//一对一
    })
    List<AccountUser> findAll();

    @Select ("select * from account where uid=#{uid}")
    List<Account> findByUid(int uid);
}
```

```java
package dao;

import doMain.User;
import doMain.UserAccount;
import org.apache.ibatis.annotations.*;
import org.apache.ibatis.mapping.FetchType;

import java.util.List;

public interface UserDao {
    @Select ("select * from user")
    @Results(id="default",value = {
            @Result(id=true,property = "id",column = "id"),
            @Result(property = "username",column = "username"),
            @Result(property = "password",column = "password"),
            @Result(property = "accounts",column = "id",many = @Many(select = "dao.AccountDao.findByUid",fetchType = FetchType.LAZY)),//一对多
    })
    List<UserAccount> findAll();

    @Select ("select * from user where id=#{id}")
    User findById(int id);
}
```

`4`二级缓存

1）主配置文件中

```xml
<settings>
	<setting name="cacheEnabled" value="true"></setting><!--默认开启，可省略-->
</settings>
```

2）dao接口新增注解

```java
package dao;

import doMain.User;
import doMain.UserAccount;
import org.apache.ibatis.annotations.*;
import org.apache.ibatis.mapping.FetchType;

import java.util.List;

@CacheNamespace(blocking = true)//开启缓存
public interface UserDao {
    @Select ("select * from user")
    @Results(id="default",value = {
            @Result(id=true,property = "id",column = "id"),
            @Result(property = "username",column = "username"),
            @Result(property = "password",column = "password"),
            @Result(property = "accounts",column = "id",many = @Many(select = "dao.AccountDao.findByUid",fetchType = FetchType.LAZY)),
    })
    List<UserAccount> findAll();

    @Select ("select * from user where id=#{id}")
    User findById(int id);
}
```

> 注意
>
> 开启缓存的前提条件是，doMain里所有实体类必须实现Serializable接口，否则会报错


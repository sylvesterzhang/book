---
layout: post
title: Mybatis-Plus
tags: Mybatis-Plus
categories: backends
---

依赖、项目内配置、主键生成策略、自动填充时间、乐观锁实现方式、一般查询、分页查询、删除、执行 SQL 分析打印、条件构造器、代码生成器

**依赖**

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.3.1.tmp</version>
        </dependency>
```

**项目内配置**

`1`配置数据库

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql:///test2?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8 #mysql8以后必须配置时区，否则报错
    username: root
    password:
  profiles:
    active: dev
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl #打印日志
```

`2`定义实体类

```java
package zhanghuan.mybatisplus.Entity;

import lombok.Data;

@Data
public class Account {
    private long id;
    private String name;
    private int money;
    private Date createTime;//数据库中为create_time
    private Date updateTime;
}

```

`3`定义接口

```java
package zhanghuan.mybatisplus.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import org.springframework.stereotype.Repository;
import zhanghuan.mybatisplus.Entity.Account;

@Repository
public interface AccountMapper extends BaseMapper<Account> {
}

```

`4`入口类配置扫描 

```java
package zhanghuan.mybatisplus;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Component;

@SpringBootApplication
@MapperScan("zhanghuan.mybatisplus.mapper")
public class MybatisplusApplication {

    public static void main(String[] args) {
        SpringApplication.run (MybatisplusApplication.class, args);
    }

}

```

`5`配置xml类型的mapper配置文件

如果需要使用xml类型的mapper配置文件，需要将mapper的xml文件放置在resource下的任意命名的文件夹中，这里命名为xml；然后在Application.yaml中新增配置

```yaml
mybatis-plus:
  mapper-locations: classpath:/xml/*.xml
```

这样启动后就会将xml文件夹内的xml文件读取到内存中

`6`测试查询所有数据

```java
package zhanghuan.mybatisplus;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import zhanghuan.mybatisplus.mapper.AccountMapper;

@SpringBootTest
class MybatisplusApplicationTests {

    @Autowired
    AccountMapper accountMapper;

    @Test
    void contextLoads() {
        accountMapper.selectList (null).forEach (System.out::println);
    }

}

```

---

**主键生成策略**

需要在实体类上进行标注，未标注，默认使用ASSIGN_ID

`1`实现

```java
package zhanghuan.mybatisplus.Entity;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;


@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Account {
    @TableId(type = IdType.ASSIGN_ID)
    private long id;
    private String name;
    private int money;
    private Date createTime;//数据库中为create_time
    private Date updateTime;
}
```

```java
package zhanghuan.mybatisplus;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import zhanghuan.mybatisplus.Entity.Account;
import zhanghuan.mybatisplus.mapper.AccountMapper;

@SpringBootTest
class MybatisplusApplicationTests {

    @Autowired
    AccountMapper accountMapper;


    @Test
    void addOne(){
        Account account=Account.builder ().money (100).name ("王冬冬").build ();
        accountMapper.insert (account);
    }

}

```

在此操作前，数据库的id必须是自增的数字类型，新增后在数据库可见id是一长串数字

`2`各策略区别I(摘自官方文档)

|        值         |                             描述                             |
| :---------------: | :----------------------------------------------------------: |
|       AUTO        |                         数据库ID自增                         |
|       NONE        | 无状态,该类型为未设置主键类型(注解里等于跟随全局,全局里约等于 INPUT) |
|       INPUT       |                    insert前自行set主键值                     |
|     ASSIGN_ID     | 分配ID(主键类型为Number(Long和Integer)或String)(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextId`(默认实现类为`DefaultIdentifierGenerator`雪花算法)，注意实体类的id类型应设置为String或long(和数据库中的字段类型对应)，int会报错 |
|    ASSIGN_UUID    | 分配UUID,主键类型为String(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextUUID`(默认default方法) |
|   ~~ID_WORKER~~   |     分布式全局唯一ID 长整型类型(please use `ASSIGN_ID`)      |
|     ~~UUID~~      |           32位UUID字符串(please use `ASSIGN_UUID`)           |
| ~~ID_WORKER_STR~~ |     分布式全局唯一ID 字符串类型(please use `ASSIGN_ID`)      |

---

**自动填充时间**

`1`数据库层面填充

数据库设置默认值为CURRENT_TIMESTAMP

`2`代码层面

1）定义一个处理类实现MetaObjectHandler接口

```java
package zhanghuan.mybatisplus.handler;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.util.Date;

@Component
public class MyObjectHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName ("createTime",new Date (),metaObject);
        this.setFieldValByName ("updateTime",new Date (),metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName ("updateTime",new Date (),metaObject);
    }
}

```

2）在实体类的字段中加上@TableField

```java
package zhanghuan.mybatisplus.Entity;

import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;


@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Account {
    @TableId(type = IdType.ASSIGN_ID)
    private long id;
    private String name;
    private int money;
    @TableField(fill = FieldFill.INSERT)//插入的时候更新
    private Date createTime;
    @TableField(fill = FieldFill.UPDATE)//修改的时候更新
    private Date updateTime;
}

```

---

**乐观锁实现方式**

`1`大致思路

取出记录时，获取当前version

更新时带上这个version

执行更新时，set version=new version where version=oldversion

如果version不对，更新失败

`2`实现

1）给表新增一个数字类型字段version

2）实体类中新增version字段并加上注释@Version

```java
package zhanghuan.mybatisplus.Entity;

import com.baomidou.mybatisplus.annotation.*;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;


@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Account {
    @TableId(type = IdType.ASSIGN_ID)
    private long id;
    private String name;
    private int money;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.UPDATE)
    private Date updateTime;
    @Version//新增字段version，并加上这个注释
    private int version;
}

```

3）将注入到spring容器中

这里再入口类进行注入OptimisticLockerInterceptor对象

```java
package zhanghuan.mybatisplus;

import com.baomidou.mybatisplus.autoconfigure.MybatisPlusPropertiesCustomizer;
import com.baomidou.mybatisplus.extension.incrementer.H2KeyGenerator;
import com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;


@SpringBootApplication
@MapperScan("zhanghuan.mybatisplus.mapper")
public class MybatisplusApplication {

    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor(){//在这里注入
        return new OptimisticLockerInterceptor ();
    }
    public static void main(String[] args) {
        SpringApplication.run (MybatisplusApplication.class, args);
    }

}

```

---

**一般查询**

```java
//核心代码
        //id查询
        accountMapper.selectById (2L);

        //多id查询
        accountMapper.selectBatchIds (Arrays.asList (1L,2L,3L ));
        //hashmap查询
        Map<String,Object> map=new HashMap<> ();
        map.put ("name","张欢");
        accountMapper.selectByMap (map).forEach (System.out::println);
```

---

**分页查询**

`1`配置类中注册插件

```java
package zhanghuan.mybatisplus.configuration;

import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import com.baomidou.mybatisplus.extension.plugins.pagination.optimize.JsqlParserCountOptimize;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class mybatisplusconfiguration {
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor ( );
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser (new JsqlParserCountOptimize (true));
        return paginationInterceptor;
    }
}

```

`2`使用分页进行查询

```java
//核心代码
        Page<Account> page=new Page<> (2,2);
        accountMapper.selectPage (page,null);
        page.getRecords ().forEach (System.out::println);
```

---

**删除**

`1`普通删除

```java
//核心代码
        //单个删除
        accountMapper.deleteById (1L);

        //批量删除
        accountMapper.deleteBatchIds (Arrays.asList (2,3));

        //根据键值对删除
        Map<String,Object> map=new HashMap<> ();
        map.put ("name","zhang");
        accountMapper.deleteByMap (map);
```

`2`逻辑删除

1）表中新增deleted字段

2）实体类中加属性，并新增@TableLogic注解

```java
package zhanghuan.mybatisplus.Entity;

import com.baomidou.mybatisplus.annotation.*;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.Date;


@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Account {
    @TableId(type = IdType.ASSIGN_ID)
    private long id;
    private String name;
    private int money;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.UPDATE)
    private Date updateTime;
    @Version
    private int version;
    @TableLogic
    private int deleted;//新增deleted属性
}

```

3）配置文件中新增如下配置

```yaml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: deleted  #全局逻辑删除字段值 3.3.0开始支持，详情看下面。
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

---

**执行 SQL 分析**

1）导入依赖

```xml
        <dependency>
            <groupId>p6spy</groupId>
            <artifactId>p6spy</artifactId>
            <version>3.9.0</version>
        </dependency>
```

2）配置文件中

```yaml
spring:
  datasource:
    driver-class-name: com.p6spy.engine.spy.P6SpyDriver #com.mysql.cj.jdbc.Driver
    type: com.zaxxer.hikari.HikariDataSource
    url: jdbc:p6spy:mysql:///test2?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
```

3）新增配置文件spy.properties

```properties
reloadproperties=true
appender=com.p6spy.engine.spy.appender.Slf4JLogger
#P6SpyLogger 类全路径名
logMessageFormat=cn.e3mall.common.utils.P6SpyLogger
databaseDialectDateFormat=yyyy-MM-dd hh:mm:ss
excludecategories=info,debug,result,resultset
```

该插件有性能损耗，不建议生产环境使用

---

**条件构造器**

一个构建查询条件的对象

```java
//核心代码
        QueryWrapper queryWrapper=new QueryWrapper ();
        queryWrapper.eq("name","王冬冬");
        List<Account> list=accountMapper.selectList (queryWrapper);
        list.forEach (System.out::println);
```

---

**代码生成器**

`1`依赖

```xml
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>com.ibeetl</groupId>
            <artifactId>beetl</artifactId>
            <version>3.1.8.RELEASE</version>
        </dependency>
```

`2`代码

```java
//核心代码
        AutoGenerator mpg = new AutoGenerator();

        GlobalConfig globalConfig = new GlobalConfig();
        /*基本配置*/
        globalConfig.setOutputDir(System.getProperty("user.dir") + "/src/main/java/zhanghuan/mybatisplus");
        globalConfig.setAuthor("zhanghuan");
        globalConfig.setOpen(false);
        globalConfig.setIdType(IdType.ASSIGN_ID); //主键策略,失效需要手动去实体类设置
        globalConfig.setDateType(DateType.ONLY_DATE);//定义生成的实体类中日期类型
        mpg.setGlobalConfig(globalConfig);

        /*数据库配置*/
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql:///subject?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8");
        dsc.setDriverName("com.mysql.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("");
        mpg.setDataSource(dsc);

        /*模块配置*/
        PackageConfig pc = new PackageConfig();
        //pc.setModuleName(""); //模块名
        pc.setParent("zhanghuan.mybatisplus");//包名
        mpg.setPackageInfo(pc);

        /*策略配置*/
        StrategyConfig strategy = new StrategyConfig ();
        strategy.setNaming(NamingStrategy.underline_to_camel);//表下划线转驼峰
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);//列下划线转驼峰
        strategy.setEntityLombokModel(true);//使用lambok标记实体类
        strategy.setLogicDeleteFieldName("deleted");//标记逻辑删除
        strategy.setVersionFieldName("version");//乐观锁
        //自动填充
        TableFill gmtCreate = new TableFill ("create_time", FieldFill.INSERT);
        TableFill gmtModified = new TableFill("update_time", FieldFill.UPDATE);
        ArrayList<TableFill> tableFills = new ArrayList<TableFill> ();
        tableFills.add(gmtCreate);
        tableFills.add(gmtModified);
        strategy.setTableFillList(tableFills);
        mpg.setStrategy(strategy);

        mpg.execute();

```

---

**其他**

`1`实体类与表的关系

默认情况下，实体类的属性与表的字段应该一一对应。如果实体类中存在属性而表中没有的对应字段，会报错。需要在实体类的该属性上加`@TableField(exist = false)`注解

---

**mybatis使用相关问题**

1）插入数据报错

除了数据本身的问题外，还有可能是主键类型配置错误

```yml
#mybatis
mybatis-plus:
  global-config:
    #数据库相关配置
    db-config:
      #主键类型  AUTO:"数据库ID自增", INPUT:"用户输入ID", ID_WORKER:"全局唯一ID (数字类型唯一ID)", UUID:"全局唯一ID UUID";
      id-type: ID_WORKER
```

当id-type配置成AUTO是，执行插入时，不会携带主键方式插入，但如果数据库并未设置成主键自增，会报错

2）更新数据不生效

前端调用接口更新一条数据时，返回成功，前后端均未报错，但数据库并没有更新。此时很有可能是主键用了雪花算法生成的长整数的id，传到前端，前端将id作为数字处理后溢出。在前端调用接口更新时，传入的id值已经是溢出后的数字，后端在执行更新时，找不到该id对应的数据，从而不进行更新，也不报错

3）查询报错

```
Caused by: java.lang.ClassCastException: java.lang.Long cannot be cast to ja
```

代码在启动时不报错，一旦调用数据库查询方法时会报错，而且报错内容不指向项目中的类，指向jar包中的错误

原因:

主键类型更改带来的后遗症，如将实体类long类型的主键改为String，代码中改完了，但是mapper.xml文件中为将返回类型的long改为string导致。（注意：xml文件中是string而不是String）


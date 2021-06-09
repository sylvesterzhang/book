---
layout: post
title: redis
tags: redis
categories: backends
---

概述、数据类型、事务、乐观锁、SpringBoot中redis使用、redis实现数据自动缓存、springboot中redis事务

**概述**

`1`能做什么？

内存存储，持久化

效率高，可用于高速缓存

发布订阅

地图信息分析

计数器

`2`特性

(1)多样数据类型

(2)持久化

(3)集群

(4)事务

`3`安装

1)下载安装包

2）执行命令

```bash
yum install gcc-c++
make
make install
```

3）修改配置文件

`4`操作

切换数据库

```
select  1
```

查看所有键

```
keys * 
```

清空数据库

```
flushdb 
```

清空所有数据库

```
flushall 
```

> 注意
>
> 1.默认有16个数据库
>
> 2.redis是单线程，瓶颈是机器内存和网络带宽

---

**数据类型**

字符串(String)，散列(hash)、列表(lists)、集合(sets)、有序集合(sorted sets)

1）字符串基本操作

设置键值对

```
set name zhang
```

判断键是否存在

```
EXISTS name
```

获取键

```
get name
```

设置过期时间

```
expire name 10
```

查看过期时间

```
ttl name
```

查看类型

```
type name
```

字符类型的键值对后追加

```
append name huan
```

获取字符串类型值的长度

```
strlen name
```

字符串截取

```
getrange name 0 3
```

字符串指定部分替换

```
setrange name 1 xx
```

设置值并设置过期时间

```
setex name 3 zxxng
```

判断是否存在，如果存在就不设置，设置成功返回1，失败返回0

```
setnx name zhang
```

批量设置

```
mset k1 v1 k2 v2 k3 v3
```

批量设置判断是否存在，如果存在就不设置（这是一个原子性操作）

```
msetnx k1 v1 k4 v4
```

2）对于数字类型的字符串可以进行加减操作

步长为1的加减

```
incr num
```

```
decr num
```

自定义步长的加减

```
incrby num 10
```

```
decrby num 10
```

3）列表类型

从左侧往列表里push数据（入栈）

```
lpush list one
```

从左侧往列表里pop数据（出栈）

```
lpop list
```

从右侧往列表里push数据（入栈）

```
rpush list two
```

从右侧往列表里pop数据（出栈）

```
rpop list
```

查看列表内的数据

```
lrange 0 -1
```

通过下标获取

```
lindex list 0
```

移除指定值

```
lrem list 1 one #移除1个元素one
```

按下标移除指定个数的元素

```
ltrim list 1 2 #从下标为1开始移除2个元素
```

移除最后一个元素并添加到一个新的列表中

```
rpoplpush list mylist
```

将指定下标的值替换成另一个值，更新操作

```
lset list 0 aaa
```

指定元素后插入值

```
linsert list before world other #在word前插入other
```

4）集合类型

新增元素

```
sadd myset hello
```

查看

```
smembers myset
```

获取set集合中元素个数

```
scard myset
```

移除元素

```
srem myset hello
```

随机抽出一个元素

```
srandmember myset
```

随机删除一个元素

```
spop myset
```

将一个指定元素移入到另一个set集合中

```
smove myset myset2 zhang
```

查看集合长度

```
scard myset
```

查看两个集合的差集

```
sdiff myset1 myset2
```

查看两个集合的交集

```
sinter myset1 myset2
```

查看两个集合的并集

```
sunion myset1 myset2
```

5）哈希

设置一个hash并设置键值对

```
hset myhash f1 v1
```

获取某个hash的某个键对应的值

```
hget myhash f1
```

为某个hash设置多个键值对

```
hmset myhash f1 v1 f2 v2
```

获取某个hash的多个键对应的值

```
hmget myhash f1 f2
```

获取某个hash的所有键值对

```
hgetall myhash
```

删除某个hash的一个键值对

```
hdel myhash f1
```

获取某个hash的长度

```
hlen myhash
```

判断hash中的指定字段是否存在

```
hexists myhash f1
```

获取hash中所有的键

```
hkeys myhash
```

获取hash中所有的值

```
hvalus myhash
```

某个字段的值加一

```
hincr myhash f1 1 
```

判断hash中某个字段，如果不存在则设置

```
hsetnx myhash f5 v5
```

6）有序集合

新增一个值

```
zadd myset1 1 one
```

新增多个值

```
zadd myset1 1 one 2 two
```

查看

```
zrange myset1 0 -1
```

查看排序结果

```
zadd salary 2500 xiaohong
zadd salary 2000 wangyu
```

```
zrangebyscore salary -inf +inf withscores #显示从小到大排序结果，附带scores
```

获取元素个数

```
zcard salary
```

获取指定区间的成员数量

```
zcount myset 1 3
```

---

**事务**

Redis事务没有隔离级别的概念，单条命令式原子性的，但事务不保证原子性

```
multi #开启事务
```

```
exec #执行
```

```
discard #取消事务
```

异常

1）编译异常

事务中有命令错误，执行时会报错，不会执行

2）运行异常

事务中语法检查通过，执行时部分成功，部分失败

---

**乐观锁**

使用watch去监控一个变量，然后开启事务去对这个变量进行修改，如果在事务执行过程中这个变量被修改，会导致这个事务提交失败，达到乐观锁的效果

>注意
>
>事务执行失败，需要调用unwatch进行解锁

---

**SpringBoot中redis使用**

`1`引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

`2`直接装配使用

```java
package com.example.redis01;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;

@SpringBootTest
class Redis01ApplicationTests {
    @Autowired
    StringRedisTemplate stringRedisTemplate;//存数据时，键类型是字符串

    @Autowired
    RedisTemplate redisTemplate;//存数据时，键类型是任意类型

    @Test
    void contextLoads() {
        stringRedisTemplate.opsForValue ().set("name","zhanghuan111222");//redis中存的键是name
        System.out.println (stringRedisTemplate.opsForValue ().get ("name"));
    }

    @Test
    void test2(){
        redisTemplate.opsForValue ().set("name","zhanghuan");//redis中存的键是\xac\xed\x00\x05t\x00\x04name，通过stringRedisTemplate无法取到数据
        System.out.println (redisTemplate.opsForValue ().get ("user"));
    }

}
//其他数据类型不再赘述

```

`3`序列化

默认采用JDK序列化，要求实现Serilizable接口，性能差，二进制内容难阅读，所以需要使用json进行序列化

```java
package com.example.redis1.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.*;


@Configuration
public class RedisConfig{

    @Bean(name = "redisTemplate")
    public RedisTemplate<String, Object> getRedisTemplate(RedisConnectionFactory factory) {//使用redisTemplate序列化配置
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<String, Object>();
        redisTemplate.setConnectionFactory(factory);
        redisTemplate.setKeySerializer(new StringRedisSerializer()); // key的序列化类型

        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);

        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer); // value的序列化类型
        return redisTemplate;
    }

}
```

---

**redis实现数据自动缓存**

`1`相关依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
 <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-jdbc -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <version>2.3.4.RELEASE</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
            <version>2.3.4.RELEASE</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.21</version>
        </dependency>
```

`2`配置文件

```yaml
server:
  port: 10000
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
  redis:
    host: localhost
    port: 6379
    password: 123456
```

`3`序列化配置文件

```java
package com.example.redis1.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cache.CacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.cache.RedisCacheWriter;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.*;


@Configuration
public class RedisConfig{
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory connectionFactory) {//注解方式使用redis序列化配置
        //初始化一个RedisCacheWriter
        RedisCacheWriter redisCacheWriter = RedisCacheWriter.nonLockingRedisCacheWriter(connectionFactory);

        Jackson2JsonRedisSerializer serializer = new Jackson2JsonRedisSerializer(Object.class);

        RedisSerializationContext.SerializationPair<Object> pair = RedisSerializationContext.SerializationPair.fromSerializer(serializer);

        RedisCacheConfiguration defaultCacheConfig=RedisCacheConfiguration.defaultCacheConfig().serializeValuesWith(pair);

        return new RedisCacheManager(redisCacheWriter, defaultCacheConfig);
    }

    @Bean(name = "redisTemplate")
    public RedisTemplate<String, Object> getRedisTemplate(RedisConnectionFactory factory) {//使用redisTemplate序列化配置
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<String, Object>();
        redisTemplate.setConnectionFactory(factory);
        redisTemplate.setKeySerializer(new StringRedisSerializer()); // key的序列化类型

        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);

        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer); // value的序列化类型
        return redisTemplate;
    }

}
```

`4`入口类新增`@EnableCache`注解

```java
package com.example.redis1;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

@SpringBootApplication
@EnableCaching
public class Redis1Application {

    public static void main(String[] args) {
        SpringApplication.run(Redis1Application.class, args);
    }

}

```

`5`实体类

```java
package com.example.redis1.entity;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.io.Serializable;

@Entity
@Table(name="user")
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class User{

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    @Column(name = "name")
    private String name;

    @Column(name = "password")
    private String password;
}

```

`6`持久层接口

```java
package com.example.redis1.mapper;

import com.example.redis1.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;

public interface UserMapper extends JpaRepository<User,Long> , JpaSpecificationExecutor<User> {
    public User getUserById(Long id);//根据id获取单条数据
}

```

`7`服务层接口

```java
package com.example.redis1.service;

import com.example.redis1.entity.User;
import com.example.redis1.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.*;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
@CacheConfig(cacheNames = "userCache")//配置全局缓存的一级键名
public class UserService {
    @Autowired
    UserMapper userMapper;

    /*
    查询所有数据
     */
    @Cacheable(key = "'findall'")//配置三级键名，实际在redis中存的键名为userCache::findall，两个：表示三级
    public List<User> findAll(){//如果缓存中有数据就取缓存中的数据，否则从数据库中取数据并放置缓存
        return userMapper.findAll();
    }

    /*
    新增一条数据
     */
    @CachePut(cacheNames = "book",key="#user.id")
    @Caching(evict = {
            @CacheEvict(cacheNames = "book",key = "#user.id"),
            @CacheEvict(allEntries = true)
    })
    public User addOne(User user){//删除userCache::findall缓存,删除book:id缓存，新增book:id缓存
        userMapper.save(user);
        return user;
    }

    /*
    根据id获取一条数据
     */
    @Cacheable(cacheNames = "book",key = "#id")//键名为book::id
    public User getOne(Long id){
        User user=userMapper.getUserById(id);
        return user;//注意：这里不可用userMapper.getOne(id)，得到的代理类在反序列化过程中会报错
    }

    /*
    修改一条数据
     */
    @CachePut(cacheNames = "book",key="#user.id")
    @Caching(evict = {
            @CacheEvict(cacheNames = "book",key = "#user.id"),
            @CacheEvict(allEntries = true)
    })
    public User updateOne(Long id){//删除book:id缓存，删除userCache::findall缓存，新增book:id缓存
        User user=userMapper.getOne(id);
        user.setPassword("password");//修改密码
        userMapper.save(user);
        return user;
    }


    /*
    删除一条数据
     */
    @Caching(evict = {
            @CacheEvict(cacheNames = "book",key = "#id"),
            @CacheEvict(allEntries = true)
    })
    public void deleteOne(Long id){
        userMapper.deleteById(id);
    }
}

```

`8`常用注解

| 注解        | 含义                                                       |
| ----------- | ---------------------------------------------------------- |
| @Cacheable  | 缓存中有数据，不执行方法；没有数据，执行方法，并将结果缓存 |
| @CachePut   | 无论缓存中有无数据，都会执行方法，并将结果缓存             |
| @CacheEvict | 执行方法后，会删除缓存                                     |
| @Caching    | 缓存操作的集合                                             |

缓存内容是方法的返回值

---

**springboot中redis事务**

 Redis 事务的本质是一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。 redis事务就是一次性、顺序性、排他性的执行一个队列中的一系列命令。 

特点：

Redis事务没有隔离级别的概念： 批量操作在发送 EXEC 命令前被放入队列缓存，并不会被实际执行，也就不存在事务内的查询要看到事务里的更新，事务外查询不能看到。 

Redis不保证原子性： Redis中，单条命令是原子性执行的，但事务不保证原子性，且没有回滚。事务中任意命令执行失败，其余的命令仍会被执行。 

```java
package com.example.redis1;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;



@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class Redis1ApplicationTests {

    @Autowired
    RedisTemplate redisTemplate;

    @Test
    public void test5(){
        redisTemplate.setEnableTransactionSupport(true);//设置支持事务，设置后必须以事务方式存数据，否则不会去存数据
        redisTemplate.multi();//开启事务
        redisTemplate.opsForValue().set("name","abc");
        redisTemplate.exec();//执行事务
    }
}

```




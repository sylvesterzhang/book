---
layout: post
title: 分布式锁
tags: 分布式锁
categories: backends
---

概述、基本实现方式、基于redis实现探索、开源分布式锁

**概述**

在分布式应用中，如果存在临界资源，如果不使用锁会导致数据一致性的问题。对于 任何一个分布式系统都无法同时满足一致性（Consistency）、可用性（Availability）和分区容错性（Partition tolerance），最多只能同时满足两项 。 以，很多系统在设计之初就要对这三者做出取舍。在互联网领域的绝大多数的场景中，都需要牺牲强一致性来换取系统的高可用性，系统往往只需要保证“最终一致性”，只要这个最终时间是在用户可以接受的范围内即可。  在很多场景中，我们为了保证数据的最终一致性，需要很多的技术方案来支持，比如分布式事务、分布式锁等。有的时候，我们需要保证一个方法在同一时间内只能被同一个线程执行 。

---

**基本实现方式**

1）基于数据库实现

上锁：往数据库的一张表里插入一条指定id的数据，插入成功表示加锁成功

释放锁：通过id删除掉刚插入的数据

2）基于redis实现

上锁：使用setnx命令存入一个键值对，存入成功表示上锁成功

释放锁：删除掉刚插入的键值对

---

**基于redis实现探索**

`1`简单实现

```java
package com.example.redis1.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class Lock {

    @Autowired
    RedisTemplate redisTemplate;

    @GetMapping("/resource")
    public String getResource() throws InterruptedException {
        //创建、获取锁
        String key="lock";
        boolean lock=redisTemplate.opsForValue().setIfAbsent(key,"adsadasd");

        //判断是否拿到锁，如果没拿到返回错误
        if(!lock){
            System.out.println("未拿到锁，返回错误");
            return "error";
        }
        
        System.out.println("拿到锁");

        //操作临界资源
        Thread.sleep(10000);

        //释放锁
        System.out.println("操作临界资源成功，释放锁");
        redisTemplate.delete(key);

        return "get resource success";
    }
}

```

存在问题：

一个线程在操作临界资源时，此时线程挂掉，将会导致锁不能被是否，而其他线程将无法拿到锁，造成死锁（解决办法是设置锁的过期时间），try…catch…finnally也不可解决该问题



`2`升级版

```java
package com.example.redis1.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.concurrent.TimeUnit;

@RestController
public class Lock {

    @Autowired
    RedisTemplate redisTemplate;

    @GetMapping("/resource")
    public String getResource() throws InterruptedException {
        //创建、获取锁
        String key="lock";
        boolean lock=redisTemplate.opsForValue().setIfAbsent(key,"adsadasd",5, TimeUnit.SECONDS);

        //判断是否拿到锁，如果没拿到返回错误
        if(!lock){
            System.out.println("未拿到锁，返回错误");
            return "error";
        }
        
        System.out.println("拿到锁");

        //操作临界资源,此过程中锁的过期时间到了，会释放锁，另一线程拿到锁进入
        Thread.sleep(10000);

        //释放锁，会将正在执行线程的锁释放掉，导致锁失效
        System.out.println("操作临界资源成功，释放锁");
        redisTemplate.delete(key);

        return "get resource success";
    }
}

```

存在问题：

(1)线程在执行过程中，锁过期时间到而导致锁失效，其他线程会进入

(2)当线程在执行最后执行是否锁操作时，会是否其他线程的锁，最终导致锁彻底失效（解决办法是在线程执行过程中，锁的过期时间内，间隔地增加锁的过期时间）

有一种解决方案是，将锁的值设置成uuid，在释放锁时判断uuid是否和创建锁时一致，一致则是否，不一致则不做任何操作；虽然解决了线程执行释放其他线程锁的问题，但不可解决使用临界资源中锁失效的问题



`3`最终版

```java
package com.example.redis1.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.concurrent.TimeUnit;

@RestController
public class Lock {

    @Autowired
    RedisTemplate redisTemplate;

    @GetMapping("/resource")
    public String getResource() throws InterruptedException {
        //创建、获取锁
        String key="lock";
        boolean lock=redisTemplate.opsForValue().setIfAbsent(key,"adsadasd",5, TimeUnit.SECONDS);

        //判断是否拿到锁，如果没拿到返回错误
        if(!lock){
            System.out.println("未拿到锁，返回错误");
            return "error";
        }
        
        System.out.println("拿到锁");

        //开启守护线程，间隔2秒给锁延期
        Thread thread=new Thread(()->{
            while (true){
                try {
                    Thread.sleep(2000);
                    redisTemplate.expire("lock",2,TimeUnit.SECONDS);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        thread.setDaemon(true);
        thread.start();

        //操作临界资源
        Thread.sleep(10000);

        //释放锁
        System.out.println("操作临界资源成功，释放锁");
        redisTemplate.delete(key);

        return "get resource success ;";
    }
}

```

最好是将业务过程和释放锁的过程用try…catch…finnally包裹

---

**开源分布式锁**

`1`redisson

1）依赖

```xml
        <dependency>
           <groupId>org.redisson</groupId>
            <artifactId>redisson</artifactId>
            <version>3.11.1</version>
        </dependency>
```

2）配置类

```java
package com.example.redis1.config;

import org.redisson.config.Config;
import org.redisson.Redisson;
import org.redisson.api.RedissonClient;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class LockConfig {
    @Bean
    public RedissonClient redissonClient() {
        Config config = new Config();
        config.useSingleServer()
                .setAddress("redis://localhost:6379")
                .setPassword("123456");

        RedissonClient redisson = Redisson.create(config);
        return redisson;
    }
}

```

3）使用

```java
package com.example.redis1.controller;

import org.redisson.api.RLock;
import org.redisson.api.RedissonClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;


@RestController
public class Lock {

    @Autowired
    RedissonClient redisson;

    @GetMapping("/resource")
    public String getResource() throws InterruptedException {
        //创锁锁对象
        String key= UUID.randomUUID().toString().replace("-","");
        RLock lock=redisson.getLock(key);

        //获取锁
        lock.lock();
        System.out.println("拿到锁");

        //操作临界资源
        Thread.sleep(1000);

        //释放锁
        System.out.println("操作临界资源成功");

        lock.unlock();

        return "get resource success ;";
    }
}

```

`2`integration

1）依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-integration</artifactId>
            </dependency>
        <dependency>
            <groupId>org.springframework.integration</groupId>
             <artifactId>spring-integration-redis</artifactId>
         </dependency>
```

2）配置文件

```java
package com.example.redis1.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.integration.redis.util.RedisLockRegistry;

@Configuration
public class LockConfig {

    @Bean
     public RedisLockRegistry redisLockRegistry(RedisConnectionFactory redisConnectionFactory) {
        return new RedisLockRegistry(redisConnectionFactory, "REDIS_LOCK");
    }

}

```

3）使用

```java
package com.example.redis1.controller;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.integration.redis.util.RedisLockRegistry;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;
import java.util.concurrent.locks.Lock;


@RestController
public class Lock1 {

    @Autowired
    RedisLockRegistry redisLockRegistry;

    @GetMapping("/resource1")
    public String getResource() throws InterruptedException {
        //创锁锁对象
        String key= UUID.randomUUID().toString().replace("-","");
        Lock lock=redisLockRegistry.obtain(key);

        //获取锁
        lock.lock();
        System.out.println("拿到锁");

        //操作临界资源
        Thread.sleep(1000);

        //释放锁
        System.out.println("操作临界资源成功");

        lock.unlock();

        return "get resource success ;";
    }
}

```

这种方式会在控制台打出警告

```
The UNLINK command has failed (not supported on the Redis server?); falling back to the regular DELETE command
```


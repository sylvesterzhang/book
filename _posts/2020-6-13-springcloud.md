---
layout: post
title: SpringCloud
tags: java
categories: backends
---

理论知识、Eureka注册中心、Ribbon负载均衡、Hystrix熔断器、Feign服务调用、Zuul网关

**理论知识**

`1`集中式架构存在问题

代码耦合，开发维护昆仑

无法对不同模块进行针对性优化

无法水平扩展

容错率低，并发能力查

`2`微服务

一种架构模式，即一种架构方格，提倡将单一应用程序划分成一组小的服务

1）特点

单一职责：每个服务队医唯一的业务

微：服务拆分颗粒小

面向服务：每个服务都要对外暴露RestfulApi

自治：服务间相互独立，互相不干扰

独立：技术独立，团队独立，前后端分离、数据库分离、部署独立

2）优点

耦合低，易于维护，支持多语言

3）缺点

运维难度大

`3`springcloud和springboot区别

前者是关注全局服务治理的框架，后者专注于快速、便捷海华单个个体微服务

`4`远程调用方式对比

| 方式 | 协议     | 特点             | 框架         |
| ---- | -------- | ---------------- | ------------ |
| RPC  | 基于TCP  | 速度快、效率高   | dubbo        |
| Http | 基于HTTP | 消息臃肿，但灵活 | spring cloud |

`5`微服务常用组件

| 组件名  | 解释     |
| ------- | -------- |
| Eureke  | 注册中心 |
| Zuul    | 服务网关 |
| Ribbon  | 负载均衡 |
| Feign   | 服务调用 |
| Hystrix | 熔断器   |

---

**Eureka注册中心**

`1`RestTemplate获取接口数据

需要在启动类中向容器注入RestTemplate对象，然后在控制器中使用

```java
package zhanghuan.client.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;


@RestController
public class bookController {
    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/booklist")
    public String booklist(){
        String str=restTemplate.getForObject ("http://127.0.0.1:8080/api/book", String.class);//发送请求，获取结果
        System.out.println (str);
        return str;
    }
}

```

`2`Eureka原理

Eureke:服务注册中心，对外暴露自己的地址

提供者：启动后向Eureke注册自己的信息

消费者：向Eureke订阅服务

心跳（续约）：提供者会定期向Eureke发送自己的状态

1）服务注册中心

(1)核心依赖

```xml
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>

```

(2)入口类标识Eureka注册中心

```java
package com.example.springcloud3;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer//标识这个服务是Eureka注册中心
public class Springcloud3Application {

    public static void main(String[] args) {
        SpringApplication.run (Springcloud3Application.class, args);
    }

}
```

(3)配置文件

```yaml
server:
  port: 10000
spring:
  application:
    name: eureka-server
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10000/eureka
  instance:
    ip-address: 127.0.0.1
    prefer-ip-address: true
```

2）服务提供者

(1)核心依赖

```xml
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

(2)入口类标识Eureka客户端

```java
package com.example.server;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import tk.mybatis.spring.annotation.MapperScan;

@SpringBootApplication
@EnableDiscoveryClient//标记客户端
@MapperScan("com.example.server.mapper")
public class ServerApplication {

    public static void main(String[] args) {
        SpringApplication.run (ServerApplication.class, args);
    }

}

```

(3)配置文件

```yaml
server:
  port: 8081
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10000/eureka
  instance:
    prefer-ip-address: true
    ip-address: 127.0.0.1

spring:
  application:
    name: book-service
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test1?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
    username: root
    password:
```

3）服务消费者

(1)核心依赖

```xml
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

(2)入口类标识Eureka客户端

```java
package com.example.client;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
@EnableDiscoveryClient//标识客户端
public class ClientApplication {
    @Bean//注入RestTemplate
    public RestTemplate restTemplate(){
        return new RestTemplate ();
    }

    public static void main(String[] args) {
        SpringApplication.run (ClientApplication.class, args);
    }

}

```

(3)配置文件

```yaml
server:
  port: 81
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10000/eureka
  instance:
    prefer-ip-address: true
    ip-address: 127.0.0.1
```

(4)控制器

```java
package com.example.client.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.List;

@RestController
public class clientController {
    @Autowired
    DiscoveryClient discoveryClient;
    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/booklist")
    public List getBooklist(){
        ServiceInstance instance= discoveryClient.getInstances ("book-service").get (0);//从数据中心拉取服务
        String url=instance.getUri ()+ "/api/booklist";
        return restTemplate.getForObject (url,List.class);//将拉去到的数据返回
    }
}

```

4）服务列表拉去时间设置

```yaml
eureka:
  client:
    registry-fetch-interval-seconds: 30
```

5）心跳时间设置

```yaml
eureka:
  instance:
    lease-expiration-duration-in-seconds: 90
    lease-renewal-interval-in-seconds: 30
```

服务续约时间，默认2为30秒；服务失效时间，默认为90秒。

6）自我保护

服务为按时进行心跳续约，当心跳续约比例低于85%时，Eureka会提示但不会剔除服务

```yaml
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10000/eureka
  instance:
    ip-address: 127.0.0.1
    prefer-ip-address: true
  server:
    enable-self-preservation: false #关闭自我保护
    eviction-interval-timer-in-ms: 1000 #扫描失效服务的间隔时间
```

7）注册中心不进行自我注册配置

```yaml
eureka:
  client:
    register-with-eureka: false
```

---

**Ribbon负载均衡**

`1`负载均衡算法

随机、轮询、哈希

`2`使用

1）方式一

服务消费者

(1)核心依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
        </dependency>
```

(2)控制器

```java
package com.example.client.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.netflix.ribbon.RibbonLoadBalancerClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.List;

@RestController
public class clientController {
    @Autowired
    RibbonLoadBalancerClient ribbonLoadBalancerClient;//注入ribbon客户端

    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/booklist")
    public List getBooklist(){
        ServiceInstance instance= ribbonLoadBalancerClient.choose ("book-service");//拉取服务
        String url=instance.getUri ()+ "/api/booklist";
        return restTemplate.getForObject (url,List.class);
    }
}

```

2）方式二

服务消费者

(1)核心依赖

一样

(2)入口类

```java
package com.example.client;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
@EnableDiscoveryClient
public class ClientApplication {
    @Bean
    @LoadBalanced//标识负载均衡,会通过拦截器拦截resttemplate发出的请求
    public RestTemplate restTemplate(){
        return new RestTemplate ();
    }

    public static void main(String[] args) {
        SpringApplication.run (ClientApplication.class, args);
    }

}

```

(3)控制器

```java
package com.example.client.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.List;

@RestController
public class clientController {

    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/booklist")
    public List getBooklist(){
        String url="http://booke-service/api/booklist";//地址直接写服务名
        return restTemplate.getForObject (url,List.class);
    }
}

```

---

**Hystrix熔断器**  

`1`雪崩问题

一次请求调用多个服务，其中某个服务出现异常，将导致请求阻塞，此时tomcat不会释放该线程，最终导致越来越多线程阻塞，直至资源耗尽。通过线程隔离和服务熔断可解决。

`2`Hystrix解决方式

Hystrix为每个依赖服务调用分配一个小的线程池，用户的请求将不再直接访问服务，而是通过线程池中的空闲线程来访问服务，如果线程池已满或请求超时时，则会进行降级处理

`3`降级处理

 当服务器压力剧增的情况下，优先保证核心服务，而非核心服务不可用或弱可用

`4`使用

服务消费者

1）核心依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
```

2）入口类标识熔断

```java
package com.example.client;

import org.springframework.boot.SpringApplication;
import org.springframework.cloud.client.SpringCloudApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

//@SpringBootApplication
//@EnableDiscoveryClient
//@EnableCircuitBreaker//加入熔断
//三者可以合并为以下注解
@SpringCloudApplication
public class ClientApplication {
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate ();
    }

    public static void main(String[] args) {
        SpringApplication.run (ClientApplication.class, args);
    }

}

```

3）控制器中标识熔断请求的回调方法

(1)一般

```java
package com.example.client.controller;

import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.ArrayList;
import java.util.List;

@RestController
public class clientController {

    @Autowired
    RestTemplate restTemplate;

    @HystrixCommand(fallbackMethod ="getBooklisterr" )//配置熔断后的回调方法，回调方法只可为无参方法，否则报错。
    @GetMapping("/booklist")
    public List getBooklist(){
        String url="http://book-service/api/booklist";
        return restTemplate.getForObject (url,List.class);
    }
    public List getBooklisterr(){//定义熔断的回调方法
        List<String> list=new ArrayList<> ();
        list.add ("服务器很忙，请稍后再试");
        return list;
    }
}

```

(2)通用回调方法

```java
package com.example.client.controller;

import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.ArrayList;
import java.util.List;

@RestController
@DefaultProperties(defaultFallback ="getBooklisterr" )//为这个控制器设定统一的熔断回调
public class clientController {

    @Autowired
    RestTemplate restTemplate;

    @HystrixCommand
    @GetMapping("/booklist")
    public List getBooklist(){
        String url="http://book-service/api/booklist";
        return restTemplate.getForObject (url,List.class);
    }
    public List getBooklisterr(){//定义熔断的回调方法,这里不可有参数
        List<String> list=new ArrayList<> ();
        list.add ("服务器很忙，请稍后再试");
        return list;
    }
}

```

(3)自定义熔断的超时时长

```java
package com.example.client.controller;

import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.ArrayList;
import java.util.List;

@RestController
@DefaultProperties(defaultFallback ="getBooklisterr",commandProperties = {
        @HystrixProperty (name = "execution.isolation.thread.timeoutInMilliseconds",value = "1")//设置超时时间为1毫秒
})
public class clientController {

    @Autowired
    RestTemplate restTemplate;

    @HystrixCommand
    @GetMapping("/booklist")
    public List getBooklist(){
        String url="http://book-service/api/booklist";
        return restTemplate.getForObject (url,List.class);
    }
    public List getBooklisterr(){
        List<String> list=new ArrayList<> ();
        list.add ("服务器很忙，请稍后再试");
        return list;
    }
}

```

也可以在配置文件中进行全局配置

```yaml
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 1
```

`5`服务熔断器

`1`三个状态

| 状态     | 中文 | 解释                                                         |
| -------- | ---- | ------------------------------------------------------------ |
| closes   | 关闭 | 正常情况下处于关闭状态                                       |
| open     | 打开 | 失效请求达到阈值（默认20次50%），会触发                      |
| haffopen | 半开 | 熔断一段时间后，会释放一部分请求，如果健康则会关闭熔断器，否则再次打开熔断器 |

`2`配置

```java
package com.example.client.controller;

import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.ArrayList;
import java.util.List;

@RestController
@DefaultProperties(defaultFallback ="getBooklisterr",commandProperties = {
        @HystrixProperty (name="circuitBreaker.requestVolumeThreshold",value = "10"),//触发熔断的最小请求次数，默认20
        @HystrixProperty (name="circuitBreaker.errorThresholdPercentage",value = "50"),//触发熔断的失败请求最小占比，默认是50%
        @HystrixProperty (name="circuitBreaker.sleepWindowInMilliseconds",value = "5000")//休眠时长，默认时5000毫秒
})
public class clientController {

    @Autowired
    RestTemplate restTemplate;

    @HystrixCommand
    @GetMapping("/booklist")
    public List getBooklist(){
        String url="http://book-service/api/booklist";
        return restTemplate.getForObject (url,List.class);
    }
    public List getBooklisterr(){
        List<String> list=new ArrayList<> ();
        list.add ("服务器很忙，请稍后再试");
        return list;
    }
}

```

---

**Feign服务调用**

把请求伪装，不再拼接url

`1`开始

服务消费者

1）核心依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```

openfeign里面包含ribbon和hystrix，但这两个依赖可以必须导入

2）启动类配置feign

```java
package com.example.client;

import org.springframework.boot.SpringApplication;
import org.springframework.cloud.client.SpringCloudApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;


@SpringCloudApplication
@EnableFeignClients//标识使用feign
public class ClientApplication {
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate ();
    }

    public static void main(String[] args) {
        SpringApplication.run (ClientApplication.class, args);
    }

}

```

3）定义feign的接口

```java
package com.example.client.feign;

import com.example.client.entity.Book;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

import java.util.List;

@FeignClient("book-service")//服务的地址
public interface BookClient {
    @GetMapping("api/booklist")//请求链接
    List getbooks();//请求结果封装到这个方法里
    @GetMapping("api/book/{id}")
    Book queryId(@PathVariable("id") int id);
}

```

4）控制器中使用feign

```java
package com.example.client.controller;

import com.example.client.entity.Book;
import com.example.client.feign.BookClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;


@RestController
public class clientController {
    
    @Autowired
    BookClient bookClient;//注入feign接口

    @GetMapping("/booklist")
    public List getBooklist(){
        return bookClient.getbooks ();//调用接口中定义的方法
    }

    @GetMapping("/book/{id}")
    public Book getBook(@PathVariable("id") int id){
        return bookClient.queryId (id);
    }
}

```

`2`熔断和负载均衡

1）配置信息

```yaml
ribbon:
  connectionTimeOut: 500 #连接时间
  ReadTimeOut: 2000 #读取时间

feign:
  hystrix:
    enabled: true #是否开启熔断，开启后配置熔断类才有效
```

2）类的方式配置熔断

(1)实现刚刚定义feign的接口

```java
package com.example.client.feign;

import com.example.client.entity.Book;
import org.springframework.stereotype.Component;

import java.util.ArrayList;
import java.util.List;

@Component
public class BookClientImpl implements BookClient {//实现feign接口，重写的方法就是回调方法
    @Override
    public List getbooks() {
        List list=new ArrayList ();
        list.add ("人数过多，请稍后再试1");
        return list;
    }

    @Override
    public Book queryId(int id) {
        Book book=new Book ();
        return book;
    }
}

```

(2)在feign接口将回调类注册

```java
package com.example.client.feign;

import com.example.client.entity.Book;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

import java.util.List;

@FeignClient(value = "book-service",fallback = BookClientImpl.class)//将回调指向其实现类
public interface BookClient {
    @GetMapping("api/booklist")
    List getbooks();
    @GetMapping("api/book/{id}")
    Book queryId(@PathVariable("id") int id);
}

```

`3`feign的其他配置

```yaml
feign:
  hystrix:
    enabled: true #是否允许熔断
  compression:
    request:
      enabled: true #开启请求压缩
    response:
      enabled: true #是否开启压缩
      mime-types: text/html #压缩类型
      min-response-size: 2048 #触发压缩的下限
```

---

**Zuul网关**

核心是一个过滤器，可完成身份认证、动态路由、负载均衡

`1`作为路由

1）快速开始

(1)核心依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId><!--需要从注册中心拉取服务-->
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>
```

(2)配置文件

```yaml
server:
  port: 82
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10000/eureka
      register-with-eureka: false
spring:
  application:
    name: zuul-client

zuul:
  routes:
    hehe:
      path: /book-service/**
      url: http://127.0.0.1:8081
```

这里的zuul相关的配置可以不配置，从注册中心拉取服务后，会自动生成路由，path值是服务的名称

(3)启动类

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run (DemoApplication.class, args);
    }

}

```

2）为服务配置全局前缀

```yaml
zuul:
  prefix: /client
```

未配置之前访问http://127.0.0.1:82/book-service/api/booklist，配置之后访问http://127.0.0.1:82/client/book-service/api/booklist

3）忽略服务

不想为某些服务自动设置路由

```yaml
zuul:
  ignored-services:
    -book-service
```

`2`作为过滤器

1）Zuul中定义了四种标准过滤器类型

(1) PRE：这种过滤器在请求被路由之前调用。我们可利用这种过滤器实现身份验证、在集群中选择请求的微服务、记录调试信息等。

(2) ROUTING：这种过滤器将请求路由到微服务。这种过滤器用于构建发送给微服务的请求，并使用Apache HttpClient或Netfilx Ribbon请求微服务。

(3) POST：这种过滤器在路由到微服务以后执行。这种过滤器可用来为响应添加标准的HTTP Header、收集统计信息和指标、将响应从微服务发送给客户端等。

(4) ERROR：在其他阶段发生错误时执行该过滤器。

2）定义一个过滤器验证请求中是否有token

```java
package com.example.demo.filter;

import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;
import org.apache.commons.lang.StringUtils;
import org.apache.http.HttpStatus;
import org.springframework.cloud.netflix.zuul.filters.support.FilterConstants;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;

@Component
public class LoginFilter extends ZuulFilter {
    @Override
    public String filterType() {//过滤器类型，是前置过滤器还是后置过滤器
        return FilterConstants.PRE_TYPE;
    }

    @Override
    public int filterOrder() {//过滤器织入请求周期的位置
        return FilterConstants.PRE_DECORATION_FILTER_ORDER-1;
    }

    @Override
    public boolean shouldFilter() {//是否过滤
        return true;
    }

    @Override
    public Object run() throws ZuulException {
        RequestContext ctx=RequestContext.getCurrentContext ();
        HttpServletRequest request=ctx.getRequest ();
        String token=request.getParameter ("token");
        System.out.println (token);
        if(StringUtils.isBlank (token )){
            ctx.setSendZuulResponse (false);//在请求上下文中标记不通过
            ctx.setResponseStatusCode (HttpStatus.SC_FORBIDDEN);//设置返回代码
        }
        return null;
    }
}

```

3）配置hystrix和robbon

由于zuul内部集成了hystrix和robbon，默认情况下熔断超时时间只有1秒，易触发，需要在配置文件进行配置

```yaml
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 6000
ribbon:
  connectionTimeOut: 500 #连接时间
  ReadTimeOut: 2000 #读取时间  
```

ribbon超时时长，真实值(read+connect)*2，必须要小于熔断时长
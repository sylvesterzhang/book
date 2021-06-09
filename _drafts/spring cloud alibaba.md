**不同类型架构对比**

1）单体应用架构

优点

架构简单，对小型项目来讲，开发维护简单，部署再一个单点tomcat上，后期维护方便

缺点

对大型项目来讲，维护困难，模块间紧密耦合，单点容错率低，无法针对某一个模块进行水平扩展或优化

2）垂直应用架构

优点

系统拆分后，可以进行水平扩展和优化，提高单点容错性

缺点

系统间无法相互调用，会有部分重复代码

3）分布式架构

优点

抽取公共代码为服务层，增强代码复用性

缺点

调用关系复杂，维护困难

4）SOA架构

优点

使用服务治理中序帮助维护复杂的调用关系

缺点

服务由依赖性，可能会因为一个服务的问题，导致多个系统不可用（拆分不够彻底）

5）微服务架构常见概念

服务治理

服务的自动化管理，核心是服务的自动注册与发现

---

**微服务组件**

1）Sentinel

把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性

2）Nacos

服务发现、配置管理和服务管理平台

3）RoketMQ

开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务

4）Dubbo

高性能的RPC框架

5）Seata

易于使用的高性能微服务分布式解决方案

6）Alibaba Cloud ACM

分布式架构环境中对应用配置进行集中管理和推送的应用配置中心产品

7）Alibaba Cloud OSS

阿里云对象存储服务，阿里云提供的海量、安全、低成本、高可靠的云存储服务

8）Alibaba Cloud SchedulerX

分布式任务调度产品，提供任务调度服务

9）Alibaba Cloud SMS

覆盖全球的短信服务

10）基础依赖

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>0.9.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Greenwich.SR2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

---

**nacos**

`1`依赖

```xml
<dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
```

`2`注册服务

1）启动nacos注册中心

2）application.yaml进行如下配置

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password:
    url: jdbc:mysql:///test?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8

  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848 #注册的目标地址
  application:
    name: test-service #应用名，一定要标注，否则无法注册；不可有下划线，否则客户端报错
```

`3`服务调用

服务提供者和消费者都需要进行如上设置，所以都会被注册中心抓取

1）启动类上将`RestTemplate`注入容器

```java
package com.example.nacos_consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class ConsumerApplication {

    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }

}

```

2）通过服务发现客户端获取实例

```java
package com.example.nacos_consumer.controller;

import com.example.nacos_consumer.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.List;

@RestController
public class UserController {

    @Autowired
    RestTemplate restTemplate;//注入restTemplate

    @Autowired
    DiscoveryClient discoveryClient;//注入服务发现客户端

    @GetMapping("/getuser")
    public User getUser(){
        //获取服务实例
        List<ServiceInstance> instances=discoveryClient.getInstances("test-service");
        if(instances.size()==0){
            throw new RuntimeException("没有服务");
        }
        String url=instances.get(0).getUri()+"";//从服务实例中取出服务的地址
        return restTemplate.getForObject(url,User.class);
    }

}

```

`4`搭载集群

将所有nacos连接到同一个数据库，然后再cluster.conf中将所有nacos的ip节点写进去，最后再通过nginx做负载均衡

> 注意
>
> 需要将服务注册到所有的nacos中，能实现集群效果，但不是真正的集群，这个方式会有些冗余；另一种方式就是使用集群模式启动，只需要将服务注册到其中一个nacos即可

CAP定律概念

一致性（C）：在分布式系统中，如果服务器是集群的模式下，每个节点同一时刻查询的数据必须保持一致性

可用性（A）：集群节点中，部分节点出现故障后仍然可以继续使用

分区容错性（P）：分布式系统中网络存在脑裂问题，部分的server与整个集群失去联系无法形成一个群体

三者不可兼顾，需要取舍，分为CP和AP

`5`Zookeeper、Eureka、nacos对比

| Zookeeper                                                    | Eureka                                                       | nacos              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------ |
| Zookeeper采用CP保证数据的一致性问题，原理采用Zab原子广播协议，当我们的zk领导因为某种原因宕机情况下，会自动触发重新选一个新的领导角色，整个选举的过程为了保证数据的一致性问题，在选举过程中整个zk环境是不可用使用，可以短暂可能无法使用zk。在节点出现宕机情况，需要满足过半机制，整个zk才可使用 | Eureka采用AP的设计理念，完全去中心化，每个节点均等，采用相互注册原理，只要最后有一个节点存在就可保证整个微服务可以实现通讯 | 混合AP和CP，默认AP |

`6`主节点选举机制

1）ZAP协议

实现原理时通过比较myid，值最大的可能时领导角色，只要满足过半的机制就可以成为领导角色，后来启动的节点不会参与选举

每个节点有个zxid和myid，每次同步数据后zxid会自增。当收到一个写的命令后，主节点先将数据写入，再发送ack给从节点，反馈过半时才会把数据发给从节点。如果主节点宕机后，从节点会进行选举。先比较zxid，再比较myid，值大者选举未主节点。

2）Raft协议

分为3种状态，跟随者、竞选者、领导；竞选者票数最多的会成为领导。

在服务器集群式偶数的情况下，会出现竞选者票数一致情况，会作废后重新竞选。

默认情况下，每个节点都是跟随者，每个节点会随机生成一个选举超时时间，大致100-300ms；超时时间过后，当前的节点的状态可能有跟随者变为竞选者，同时给其他节点发出选举投票通知，只要该竞选者有超过半数以上投票即可成为领导角色

`7`配置中心

1）Bootstrap与application区别

在 Spring Boot 中有两种上下文，一种是 bootstrap, 另外一种是 application, bootstrap 是应用程序的父上下文，也就是说 bootstrap 加载优先于 applicaton。bootstrap 主要用于从额外的资源来加载配置信息，还可以在本地外部配置文件中解密属性。这两个上下文共用一个环境，它是任何Spring应用程序的外部属性的来源。bootstrap 里面的属性会优先加载，它们默认也不能被本地相同配置覆盖。

2）快速开始

(1)依赖

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-config -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>2.2.1.RELEASE</version>
</dependency>
```

(2)创建配置文件bootstrap.yaml

```yaml
spring:
  cloud:
    nacos:
      config:
        server-addr: localhost:8848
        file-extension: yaml #配置中心里配置文件的后缀
  application:
    name: user-service #应用名，默认情况会根据这个去配置中心找user-service.yaml的配置文件
```

(3)在控制器上添加`@RefreshScope`注解，保证配置中心配置文件更新会同步到应用中

在nacos后台页面创建名(Data Id)为user-service.yaml的配置文件，并将配置写入

(4)启动应用，会从bootsrap.yaml中读取配置文件，再去注册中心读取配置文件，读取内容会覆盖application.yaml中的内容

> 注意
>
> 配置中心的配置会覆盖application.yaml中的内容
>
> 与nacos相关的配置一定要放在bootstrap.yaml中，否则配置中心不会生效
>
> 以user-service.yaml的配置文件支持热更新

(5)共享配置

要实现多个服务共享配置中心的一个配置文件，需要先在配置中心定义好一个任意命名的配置文件，在应用的bootstrap.yaml中指向这个配置文件

```yaml
spring:
  cloud:
    nacos: 
      config:
        server-addr: localhost:8848
        file-extension: yaml
        shared-configs: user.yaml,abc.yaml #共享配置文件
  application:
    name: user-service 
```

> 注意：
>
> 共享配置文件不支持热更新

(6)持久化

1）将nacos/conf里的nacos-mysql导入到数据库

2）在nacos/conf/application.properties新增配置

```properties
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=
```

配置中心的所有配置文件将会被持久化到数据库中

---

**容错核心思想**

`1`目的

1.保证自己不被上游服务压垮

2.保证自己不被下游服务拖垮

3.保证外界环境良好

`2`容错思路

1）隔离

按一定规则划分若干个服务模块，个模块相互独立，无强依赖，当由故障发生，能将问题和影响隔离在某个模块内部，而不扩散。常见隔离方式有：线程池隔离和信号量隔离

2）超时

在上游服务调用下游服务时，设置一个最大响应时间，如果超过这个时间，下游未作出反应，就断开请求，释放掉线程

3）限流

限制输入和输出流量，以达到保护系统目的

4）熔断

当下游服务因访问压力过大而响应变慢或失败，上游服务为保护系统整体可用性，可暂时切断对下游服务的调用

5）降级

为服务提供一个托底方案，一旦服务无法正常使用，就使用托底方案

---

**sentinel**

`1`理论知识

1）核心功能

流量控制、熔断降级、系统负载保护

2）限流实现算法

(1)计数器算法

指定周期内累加访问次数，达到阈值时触发限流策略，下一时间周期访问次数清零，存在临界问题

(2)滑动窗口算法

在固定窗口中分割出多个小时间窗，将总的请求限制分担到每个小时间窗，随时间推移，滑动窗口向前移动

(3)令牌桶算法

系统以恒定速度往令牌桶内放令牌，桶满后会丢弃新放入的令牌。请求只有拿到令牌才可访问。当请求速度大于令牌生成速度，令牌很快被取完，再进来的请求会被限流

3）流控

以每秒钟请求数量来控制访问，当达到流控时，请求会被拒绝，但过一段时间又会恢复

(1)三种流控模式

直接：接口达到限流条件时，开启限流

关联：当关联资源达到限流条件时，开启限流，适合应用让步

链路：当某个接口过来的资源达到限流条件时，开启限流

(2)三种流控效果

快速失败：达到阈值后，后面的请求直接抛出异常

预热：阈值逐渐增大到目标阈值

排队等待：在阈值的基础上额外增加队列，队列之外的请求抛出异常

4）降级规则

平均响应时间、异常比例、异常数量

5）热点

流控规则的参数级的细化，流控规则不仅能细化到参数，还能细化到参数值

6）资源访控

(1)根据来源控制访问

(2)系统规则

系统保护规则是从应用级别的入口流量进行控制，从单台机器的总体load、rt、入口qps、cpu使用率和线程数五个维度监控应用数据，让系统尽可能跑再最大吞吐量的同时保证系统整体稳定性

系统保护规则是应用整体维度的，而不是资源维度的，并且对入口流量生效

load 当系统load超过阈值，且系统当前的并发线程数超过系统容量时才会触发系统保护

rt 当单台机器上所有入口流量的并发线程数达到阈值时触发系统保护

线程数 当单台机器上所有入口流量的并发线程数达到阈值触发系统保护

cpu使用率 当单台机器上所有入口流量的cpu使用率达到阈值即触发保护

`2`快速实践限流

1）依赖

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-sentinel -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
    <version>2.2.1.RELEASE</version>
</dependency>
```

导入依赖后就完成容错配置

2）配置文件中新增

```yaml
server:
  port: 8081
spring:
  cloud:
    sentinel:
      transport:
        port: 9999 #随意指定一个未被占用的端口
        dashboard: localhost:8080 #指定控制台服务地址，需启动控制台的jar包
  application:
    name: nacos-consumer
```

3）定义资源及熔断处理方法

```java
package com.example.demo.controller;

import com.alibaba.csp.sentinel.annotation.SentinelResource;
import com.alibaba.csp.sentinel.slots.block.BlockException;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class Abc {
    @GetMapping("/hello")
    @SentinelResource(value = "hello",blockHandler = "helloBlockHandler")//还可以有一个fallback参数
    public String hello(){
        return "hello";
    }
    //定义异常处理方法
    public String helloBlockHandler(BlockException e){
        return "出错了";
    }
}

```

4）定义熔断规则

```java
package com.example.demo.FlowRule;

import com.alibaba.csp.sentinel.init.InitFunc;
import com.alibaba.csp.sentinel.slots.block.RuleConstant;
import com.alibaba.csp.sentinel.slots.block.flow.FlowRule;
import com.alibaba.csp.sentinel.slots.block.flow.FlowRuleManager;


import java.util.ArrayList;
import java.util.List;


public class FlowRuleInitFunc implements InitFunc {
    @Override
    public void init() throws Exception {
        List<FlowRule> rules = new ArrayList<>();
        FlowRule flowRule = new FlowRule();
        flowRule.setCount(1);
        flowRule.setResource("hello");
        flowRule.setGrade(RuleConstant.FLOW_GRADE_QPS);
        flowRule.setLimitApp("default");
        rules.add(flowRule);
        FlowRuleManager.loadRules(rules);
    }
}

```

5）加载规则

在resource目录下创建文件com.alibaba.csp.sentinel.init.InitFunc，然后将规则类的全路径写入到该文件内

```
com.example.demo.FlowRule.FlowRuleInitFunc
```

`3`授权规则

对指定来源的请求进行放行或拦截，白名单表示指定来源进行放行，黑名单表示对指定来源进行拦截

1）定义来源

```java
package com.example.demo.service;

import com.alibaba.csp.sentinel.adapter.spring.webmvc.callback.RequestOriginParser;
import io.netty.util.internal.StringUtil;
import org.springframework.stereotype.Component;


import javax.servlet.http.HttpServletRequest;

@Component
public class RequestOriginParserDefinition implements RequestOriginParser {
    @Override
    public String parseOrigin(HttpServletRequest request) {
        String serviceName = request.getParameter("serviceName");
        if(StringUtil.isNullOrEmpty(serviceName)){
            throw new RuntimeException("服务名不可为空");
        }
        return serviceName;
    }
}

```

2）指定来源并设置拦截或放行

在控制台系统规则内进行操作

`4`自定义熔断页面

默认情况下，无论是限流、降级、参数限流、授权异常、系统异常，都返回同一个页面，无法区分。可以自定义熔断页面，根据不同的熔断原因返回不同的熔断页面

```java
package com.example.demo.service;


import com.alibaba.csp.sentinel.adapter.spring.webmvc.callback.BlockExceptionHandler;
import com.alibaba.csp.sentinel.slots.block.BlockException;
import com.alibaba.csp.sentinel.slots.block.authority.AuthorityException;
import com.alibaba.csp.sentinel.slots.block.degrade.DegradeException;
import com.alibaba.csp.sentinel.slots.block.flow.FlowException;
import com.alibaba.csp.sentinel.slots.block.flow.param.ParamFlowException;
import com.alibaba.csp.sentinel.slots.system.SystemBlockException;
import com.alibaba.fastjson.JSONObject;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class ExceptionHandlerPage implements BlockExceptionHandler {//2.2.0版本，和老版本不一致
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, BlockException e) throws Exception {
        response.setContentType("application/json;charset=utf-8;");
        JSONObject jsonObject = new JSONObject();
        if(e instanceof FlowException){
            jsonObject.put("type","FlowException");
        }else if(e instanceof DegradeException){
            jsonObject.put("type","DegradeException");
        }else if(e instanceof ParamFlowException){
            jsonObject.put("type","ParameterExpression");
        }else if(e instanceof AuthorityException){
            jsonObject.put("type","AuthorityException");
        }else if(e instanceof SystemBlockException){
            jsonObject.put("type","SystemBlockException");
        }
        response.getWriter().write(JSONObject.toJSONString(jsonObject));
    }
}

```

注意：流控规则需要在控制台设置



降级规则预加载

```java
package com.example.nacos_consumer;

import com.alibaba.csp.sentinel.slots.block.RuleConstant;
import com.alibaba.csp.sentinel.slots.block.degrade.DegradeRule;
import com.alibaba.csp.sentinel.slots.block.degrade.DegradeRuleManager;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

import java.util.ArrayList;
import java.util.List;

@SpringBootApplication
@EnableFeignClients
public class ConsumerApplication {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

    public static void main(String[] args) {
        initDegradeRule();//调用降级规则
        SpringApplication.run(ConsumerApplication.class, args);
    }

    private static void initDegradeRule() {//定义降级规则
        List<DegradeRule> rules = new ArrayList<>();
        DegradeRule rule = new DegradeRule();
        rule.setResource("/getusers");//设置资源
        
        // 异常数超过1，发生熔断，10秒内不恢复
        rule.setCount(1);
        rule.setGrade(RuleConstant.DEGRADE_GRADE_EXCEPTION_COUNT);
        rule.setTimeWindow(10);
        rules.add(rule);
        
        //载入熔断规则
        DegradeRuleManager.loadRules(rules);
    }

}

```



openfeign客户端

springcloud第一代采用feign，第二代采用openfeign

openfeign客户端作用：是一个web声明式的http客户端远程调用工具，底层式是分值的httpclient技术。属于springcloutd自己研发，而feign是netflic研发。

架构设计

```
mayike-meite-openfeign-parent #整个依赖父类
	--mayiket-service-api #开发的api接口
	----mayiket-service-api-member
	----mayiket-service-api-order
	--mayiket-service-impl #api接口的实现
	----mayiket-service-impl-member
	----mayiket-service-impl-order
```

优点就是对feign实现重复使用



feign客户端向微服务发请求时，默认使用post方式，如果要用get方法需要在方法的参数前加上`@RequestParam("param")`标识是get请求

在feign中自定义异常处理逻辑

1）开启feign对sentinel的支持

```yaml
feign:
  sentinel:
    enabled: true #未开启sentinel是不会触发异常处理类进行降级的
```

2）定义异常吹逻辑的类（实现feign接口）

```java
package com.example.nacos_consumer.Feign;

import org.springframework.stereotype.Component;

@Component
public class UserClientFallback implements UserClient {
    @Override
    public String getusers() {
        return "出异常了";
    }
}

```



3）在feign接口中指定异常处理逻辑的类

```java
package com.example.nacos_consumer.Feign;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;


@FeignClient(value="user-service",fallback = UserClientFallback.class)//fallback指向异常处理逻辑的类的加载器
public interface UserClient {
    @GetMapping("/get_users")
    String getusers();
}

```

这样配置之后，在feign中自定义的异常处理逻辑才会生效，发生异常后会执行自定义的异常处理逻辑，但是异常并没有被捕获

4）使用容错处理来捕获异常（解决异常未被捕获问题）

(1)定义FallbackFactory异常处理类

```java
package com.example.nacos_consumer.Feign;

import feign.hystrix.FallbackFactory;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

@Component
@Slf4j
public class UserClientFallbackFactory implements FallbackFactory<UserClient> {
    @Override
    public UserClient create(Throwable cause) {
        return new UserClient() {
            @Override
            public String getusers() {
                log.error(cause.getMessage());
                return "出异常了from factory";
            }
        };
    }
}

```

(2)将feign接口指向异常处理类

```java
package com.example.nacos_consumer.Feign;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;


@FeignClient(value="user-service",fallbackFactory = UserClientFallbackFactory.class)//注意：不要定义fallback，否则无法执行fallbackFactory指向的异常处理类
public interface UserClient {
    @GetMapping("/get_users")
    String getusers();
}

```

---

**Spring Cloud Gateway**

`1`客户端调取服务存在的问题

1）客户端需要发起多次请求，增加了网络通信的成本

2）服务的鉴权分布在每个微服务中，每个服务的调用都要重复鉴权

3）后端服务架构如果采用不同的协议，客户端如果需要调用多个服务，需要对不同协议进行适配

`2`网关作用

1）对请求进行转发及服务的整合

2）对所有请求进行统一的鉴权、限流、熔断、日志

3）协议转化

4）统一错误码处理

5）可实现用户的验证登陆、解决跨域、日志拦截、权限控制、限流熔断、负载均衡、黑名单、白名单机制等

`3`统一认证鉴权

在网关层对请求进行拦截，获取请求中附带的用户身份信息，调用统一认证中心对请求进行身份认证，在认证之后再检查是否有资源的访问权限

`4`灰度发布
产品在高频率的迭代模式下，会伴随一些风险，如兼容问题、bug等问题，为规避这些问题，对于有较大的功能性改动，会将要发布的的功能开发一小部分给用户使用，并限制在一定范围。

`5`简单配置

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - predicates:
            - Path=/gateway/** #路径匹配
          filters: #匹配成功后，通过过滤器
            - StripPrefix=1 #去掉前缀
          uri: http://localhost:8080/hello #转发到这个地址
```

注意：springboot-starter-web和gateway不可在同一个maven中，否则项目无法运行

`6`重要概念

路由：由ID、目标uri，Predicate集合、Filter集合组成
谓语：JAVA8中的函数式接口，可匹配HTTP请求中的任何内容，只有当集合判断结果为true时，才会被当前路由进行转发
过滤器：为请求提供前置、后置的过滤

`7`Route Predicate Factories

1）时间规则匹配

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - After=2020-10-05T13:00:00.000+08:00[Asia/Shanghai] #在2020-10-05 13：00之后访问 / ,跳转到百度
          uri: http://www.baidu.com
```

2）Cookie匹配

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Cookie=name,zhanghuan #cookie中name值为zhanghuan进行跳转
          uri: http://www.baidu.com
```

3）Header匹配

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Header=X-Request-Id,\d+ #请求头中X-Request-Id值为数字进行跳转
          uri: http://www.baidu.com
```

4）Host匹配

```yml
server:  
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Host=**.zhanghuan.com #请求中Host值为**.zhanghuan.com（正则匹配）进行跳转
          uri: http://www.baidu.com
```

5）请求方法匹配

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Method=GET #请求方法为GET进行跳转
          uri: http://www.baidu.com
```

6）请求路径匹配

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Path=/gateway/ #请求路径为/gateway/进行跳转
          filters:
            - StripPrefix=1
          uri: http://www.baidu.com
```

`8`Gateway Filter Factories

1）AddRequestParameter

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Path=/gateway/** 
          filters:
            - StripPrefix=1
            - AddRequestParameter=name,zhanghuan #请求中新增name=zhanghuan这个参数
          uri: http://localhost:8080/
```

2）AdResponseHeader

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Path=/gateway/**
          filters:
            - StripPrefix=1
            - AddResponseHeader=token,123456 #响应头中新增token=123456这个参数
          uri: http://localhost:8080/
```

3）RequestRateLimiter
(1)引用依赖

```xml
<dependency>                                    <groupId>org.springframework.boot</groupId>         <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    <version>2.3.4.RELEASE</version>
</dependency>
```

redis的依赖
(2)定义一个keyreslver的类，并注入容器中

```java
package com.example.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GatewayApplication {

    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }

}
```

这个类定义了令牌桶的前缀，会在redis中生成两个键分别是

```
request_rate_limiter.{192.168.1.117}.timestamp
request_rate_limiter.{192.168.1.117}.token
```

(3)application.yml中进行如下配置

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Path=/gateway/**
          uri: http://localhost:8080/
          filters:
            - StripPrefix=1
            - name: RequestRateLimiter
              args: #当返回状态503会重试3次
                denyEmptyKey: true
                emptyKeyStatus: SERVICE_UNAVAILABLE
                keyResolver: '#{@ipAddressKeyResolver}'
                redis-rate-limiter.replenishRate: 1 #令牌当填充速度，即每秒允许执行当请求数
                redis-rate-limiter.burstCapacity: 1 #令牌桶最多可容纳当令牌数
  redis:
    host: 192.168.1.117
    port: 6379
    password: 123456
```

4）Retry

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: before
          predicates:
            - Path=/gateway/**
          filters:
            - StripPrefix=1
            - name: Retry
              args: #当返回状态503会重试3次
                retries: 3
                status: 503
          uri: http://localhost:8080/
```

`9`全局过滤器

1）LoadBalancerCientFilter

微服务负载均衡过滤器

(1)为服务指定路由

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
      routes:
        - id: loadbalance_route
          predicates:
            - Path=/hello-service/**
          uri: lb://hello-service #在nacos中注册的微服务名
          filter:
            - StripPrefix=1
    nacos: #从nacos中拉取服务
      discovery:
        server-addr: 192.168.1.117:8848
  application:
    name: gateway
```

(2)不指定路由，默认按服务名匹配

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
    nacos: #从nacos中拉取服务
      discovery:
        server-addr: 192.168.1.117:8848
  application:
    name: gateway
```

2）GatewayMetricsFilter

网关指标过滤器

(1)依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
            <version>2.3.4.RELEASE</version>
        </dependency>
```

(2)配置文件

```yml
略
```

访问 http://localhost:8888/actuator/metrics/gateway.requests

可获取到指标数据（json格式）

------

`10`自定义过滤器

1）GatewayFilter

(1)定义一个静态配置类

```java
package com.example.gateway.config;

public class GpConfig {
    private String name;
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name=name;
    }
}
```

(2)定义一个过滤器继承抽象类

```java
package com.example.gateway.filter;

import com.example.gateway.config.GpConfig;
import org.springframework.cloud.gateway.filter.GatewayFilter;
import org.springframework.cloud.gateway.filter.factory.AbstractGatewayFilterFactory;
import org.springframework.stereotype.Service;
import reactor.core.publisher.Mono;

@Service
public class GpDefineGatewayFilterFactory extends AbstractGatewayFilterFactory<com.example.gateway.config.GpConfig>
{
    public GpDefineGatewayFilterFactory() {
        super(GpConfig.class);
    }

    @Override
    public GatewayFilter apply(GpConfig config) {
        return (((exchange, chain) -> {
            System.out.println("pre ok ok ok " + config.getName());//请求进入服务器经过过滤器
            return chain.filter(exchange).then(Mono.fromRunnable(()->{
                System.out.println("post ok ok ok");//请求离开服务器经过过滤器
            }));
        }));
    }
}
```

(3)配置文件

```yml
server:
  port: 8081
spring:
  cloud:
    gateway:
      routes:
        - id: define_filter
          predicates:
            - Path=/gateway/**
          filters:
            - name: GpDefine #过滤器的前缀
              args:
                name: abcdefg #GpConfig类的name
            - StripPrefix=1
          uri: http://localhost:8080
  application:
    name: gateway
```

如果将Path定义为lb://service，请求将不会通过该过滤器，即这个过滤器不生效，故通过服务名调用服务不能使用该过滤器

2）GlobalFilter

```java
package com.example.gateway.filter;

import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.stereotype.Service;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Service
public class GbFillter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        System.out.println("请求进入服务器经过过滤器");
        return chain.filter(exchange).then(Mono.fromRunnable(()->{
            System.out.println("请求离开服务器经过过滤器");
        }));
    }

    @Override
    public int getOrder() {
        return 0;//数值越小，优先级越高
    }
}
```

无需额外配置

> 注意
> 配置nacos后，路由的远程转发应通过服务名进行转发，不可通过ip地址进行转发


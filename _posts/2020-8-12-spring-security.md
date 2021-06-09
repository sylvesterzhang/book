---
layout: post
title: Spring-Security
tags: Spring-Security
categories: backends
---

资源访问控制方式、认证流程、授权流程、快速开始、授权案例、自定义登陆登出页面、会话管理、基于方法的授权

**资源访问控制方式**

基于角色的访问控制

Role-Based Access Control

基于资源的访问控制(常用)

Resource-Based Access Control

---

**认证流程**

UsernamePasswordAuthenticationFilter

用户提交的用户名密码进行封装成Authentication，转发给AuthenticationManager

AuthenticationManager

认证任务委托给DaoAuthenticationProvider

AccessDecisionManager

DaoAuthenticationProvider

调用UserDetailsService获取用户信息、比对用户的密码是否正确，及用户是否拥有权限，将认证结果返回给UsernamePasswordAuthenticationFilter

UserDetailsService

根据用户名进行用户查询，包括用户权限

SecurityContextHolder

将用户的验证信息保存到上下文，用户会话

---

**授权流程**

FilterSecuritInterceptor

用户访问受保护的资源，调用SecurityMetadataSource查出当前资源需要的权限，再调用AccessDecisionManager进行授权决策，根据决策结果进行放行或拦截

SecurityMetadataSource

查出当前资源需要的权限

AccessDecisionManager

将用户权限和当前资源所需权限进行比对，通过投票机制决定是否允许用户访问资源

AccessDecisionManager的三个实现类

AffirmativeBased

(1)只要有AccessDecisionVoter的投票为ACCESS_GRANTED则同意用户进行访问

(2)如果全部弃权也表示通过

(3)如果没有一个人投赞成票，但是有人投反对票会抛出AccessDeniedException

ConsensusBades

(1)如果赞成票多于反对票表示通过

(2)如果反对票多于赞成票将抛出AccessDeniedException

(3)如果赞成票和反对票相同或都弃权，且allowIfEqualGrantedDeniedDecisions为true，则表示通过。否则抛出AccessDeniedException

UnanimousBased

(1)如果有一张反对票，抛出AccessDeniedException

(2)如果没有反对票，但是有赞成票，则通过

(3)如果全部弃权，且allowIfEqualGrantedDeniedDecisions为true，则表示通过。否则抛出AccessDeniedException

---

**快速开始**

`1`导入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
```

`2`application.yaml(此案例未配置)

```yaml
spring:
  security:
    user:
      name: zhanghuan
      password: 123
```

这样的设置用户的账号密码方式不常用

`3`定义配置类

```java
package com.example.testtomcat.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.password.NoOpPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class SecurityConfig  extends WebSecurityConfigurerAdapter {
    @Bean
    public PasswordEncoder passwordEncoder(){//必须要注入密码编码器
        //return new BCryptPasswordEncoder ();//常用编码器，对密码做加密处理
        return NoOpPasswordEncoder.getInstance ();//此编码器对密码不做处理
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {//指定哪些url需要进行身份验证
        http.authorizeRequests()
                .antMatchers("/hello").authenticated ()//指定url需要进行验证
                .anyRequest().authenticated ()
                .and ()
                .formLogin ();//允许表单登陆
    }
}

```

`4`定义查询用户的服务

```java
package com.example.testtomcat.service;

import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class MyUserDetailService implements UserDetailsService {//用户信息处理服务，此服务用来返回用户信息
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {//s参数是传过来的用户名，返回值是用户信息，如果未查到需要手动抛出错误
        UserDetails userDetails= User.withUsername ("zhanghuan").password ("123456").roles ("normalUser").build ();//roles属性和authorities必须二选一，否则报错
        return userDetails;
    }
}

```

如此配置之后，访问所有资源都需要进行身份验证，验证通过后才可访问资源。退出登陆需访问`/logout`

---

**授权案例**

`1`给用户授权

```java
package com.example.testtomcat.service;

import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class MyUserDetailService implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        UserDetails userDetails= User.withUsername ("zhanghuan").password ("123456").authorities ("p1").build ();//授予p1权限
        return userDetails;
    }
}

```

`2`给资源设定访问权限

```java
package com.example.testtomcat.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.password.NoOpPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class SecurityConfig  extends WebSecurityConfigurerAdapter {
    @Bean
    public PasswordEncoder passwordEncoder(){
        //return new BCryptPasswordEncoder ();
        return NoOpPasswordEncoder.getInstance ();
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/hello").authenticated ()
                .antMatchers ("/resource1").hasAuthority ("p1")//设定访问该资源必须要有p1权限
            	.antMatchers ("/resource2").access ("hasAuthority('p1') and hasAuthority(p2)")//更为灵活的授权方式
                .anyRequest()..permitAll ()
                .and ()
                .formLogin ();
    }
}

```

---

**自定义登陆登出页面**

`1`自定义登陆页面

```java
package com.example.securitytest.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.password.NoOpPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;


@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Bean
    public PasswordEncoder passwordEncoder(){
        //return new BCryptPasswordEncoder ();
        return NoOpPasswordEncoder.getInstance ();
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf ().disable ()
                .authorizeRequests()
                .antMatchers("/hello").authenticated ()
                .antMatchers ("/resource1").hasAuthority ("p1")
                .anyRequest().permitAll ()
                .and ()
                .formLogin ().loginPage ("/login-view").loginProcessingUrl ("/login");
                //这样配置后，登陆页面指向/login-view路径，登陆表单提交的地址指向/login，退出登陆的页面不复存在，但访问/logout会退出登陆（清除session）
    }
}

```

解决POST请求提交失败

1）屏蔽csrftoken

```java
package com.example.securitytest.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf ().disable ()
    }
}
```

2）提交的表单中放置csrftoken

```html
<input type="hidden" name="${_csrf.parameterName}" value="{$_csrf.token}"/>
```

`2`自定义登出页面

```java
package com.example.securitytest.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .logout()
            .logoutUrl("/logout")//登出地址
            .logoutSuccessUrl("/log-view?logout");//成功跳转
    }
}
```





---

**会话管理**

`1`会话控制

在WebSecurityConfigurerAdapter的子类中重写方法

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.sessionManagement()
            .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)
    }
```

| 机制       | 描述                                                  |
| ---------- | ----------------------------------------------------- |
| always     | 如果没有session存在就创建一个                         |
| ifRequired | 如果需要就创建一个Session（默认）                     |
| never      | SpringSecurity不创建session，如果其他地方创建就使用它 |
| stateless  | SpringSecurity不创建session，也不使用session          |

`2`会话超时

```
server.servlet.session.timeout=3600s
```

`3`安全会话

```
server.servlet.session.cookie.http-only=true
server.servlet.session.cookie.secure=true
```



---

**基于方法的授权**

```java
package com.example.securitytest.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.password.NoOpPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;


@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true,prePostEnabled = true)//方法中开启secured、prePost注解
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Bean
    public PasswordEncoder passwordEncoder(){
        //return new BCryptPasswordEncoder ();
        return NoOpPasswordEncoder.getInstance ();
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf ().disable ()
                .authorizeRequests()
                .antMatchers("/hello").authenticated ()
                .antMatchers ("/resource1").hasAuthority ("p1")
                .antMatchers ("/resource2").access ("hasAuthority('p1') and hasAuthority(p2)")
                .anyRequest().permitAll ()
                .and ()
                .formLogin ().loginPage ("/login-view").loginProcessingUrl ("/login")
                .and ()
                .logout ()
                .logoutUrl ("/logout1")
                .logoutSuccessUrl ("/login-view?logout");
    }

}

```

在方法上添加授权注解

```java
package com.example.securitytest.controller;

import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class IndexController {
    @GetMapping("/index")
    @PreAuthorize ("hasAuthority('PP')")//这里使用prePost
    public String index(Model model){

        model.addAttribute ("name","zhanghuan");
        return "index";
    }
}

```

> 注意
>
> 未标识授权的方法，不会进行权限验证
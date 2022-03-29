---
layout: post
title: shiro知识点整理
tags: shiro
categories: java
---

四个主要功能、核心概念、简单开始、自定义realm之认证、权限字符串、自定义realm之授权、shiro中授权方式、springboot整合shiro

# 四个主要的功能

**Authentication：**身份认证/登录，验证用户是不是拥有相应的身份；

**Authorization：**授权，即权限验证，判断某个已经认证过的用户是否拥有某些权限访问某些资源，一般授权会有角色授权和权限授权；

**SessionManager：**会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通JavaSE环境的，也可以是如Web环境的，web 环境中作用是和 HttpSession 是一样的；

**Cryptography：**加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；

# 核心概念

**Subject：**主体，相当与是请求过来的"用户"

**Principal：**主体的身份表示，必须具有唯一性，如用户名、手机号、邮箱地址等

**credential：**只有主题自己知道的安全信息，如密码、证书

**Token：**令牌，登录成功后为用户颁发的标识身份的数据

**SecurityManager：** 是 Shiro 的心脏；所有具体的交互都通过 SecurityManager 进行拦截并控制；它管理着所有 Subject、且负责进行认证和授权、及会话、缓存的管理

**Authenticator：**认证器，负责主体认证的，即确定用户是否登录成功，我们可以使用　 Shiro 提供的方法来认证，有可以自定义去实现，自己判断什么时候算是用户登录成功

**Authrizer：**授权器，即权限授权，给　Subject 分配权限，以此很好的控制用户可访问的资源

**Realm：**一般我们都需要去实现自己的　Realm  ，可以有1个或多个 Realm，即当我们进行登录认证时所获取的安全数据来源(帐号/密码)



# 简单开始

```java
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;

@org.junit.Test
public class Test {

    	//创建安全管理器对象
        SecurityManager securityManager = new DefaultSecurityManager();

        //给安全管理器设置realm
        ((DefaultSecurityManager)securityManager).setRealm(new IniRealm("classpath:shiro.ini"));

        //给安全管理工具设置安全管理类
        SecurityUtils.setSecurityManager(securityManager);

        //获取subject主体
        Subject subject = SecurityUtils.getSubject();

        //创建token
        UsernamePasswordToken token = new UsernamePasswordToken("zhanghuan","123456");

        //登录,如果登录失败会报错
        subject.login(token);
}
```

```ini
[users]
zhanghuan=123456
```

md5使用

```java
import org.apache.shiro.crypto.hash.Md5Hash;

@org.junit.Test
public class Test {

        Md5Hash md5Hash = new Md5Hash("123456");
        System.out.println(md5Hash.toHex());

        Md5Hash md5Hash1 = new Md5Hash("123456","aaa");
        System.out.println(md5Hash1.toHex());
		
    	//加盐后散列2048次
        Md5Hash md5Hash2 = new Md5Hash("123456","aaaa",2048);
        System.out.println(md5Hash2.toHex());
    
}
```

# 自定义realm之认证

1）普通md5

```java
package com.koal;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

public class Md5Realm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        //获取身份信息
        String principal = (String)authenticationToken.getPrincipal();

        if("zhanghuan".equals(principal)){
            return new SimpleAuthenticationInfo(principal,"7bccfde7714a1ebadf06c5f4cea752c1",this.getName());
        }
        return null;
    }
}

```

```java
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;

@org.junit.Test
public class Test {

        //创建安全管理器对象
        SecurityManager securityManager = new DefaultSecurityManager();

        //创建realm
        Md5Realm md5Realm = new Md5Realm();
        //使用hash凭证匹配器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        credentialsMatcher.setHashAlgorithmName("md5");
        md5Realm.setCredentialsMatcher(credentialsMatcher);

        //给安全管理器设置realm
        ((DefaultSecurityManager)securityManager).setRealm(md5Realm);

        //给安全管理工具设置安全管理类
        SecurityUtils.setSecurityManager(securityManager);

        //获取subject主体
        Subject subject = SecurityUtils.getSubject();

        //创建token
        UsernamePasswordToken token = new UsernamePasswordToken("zhanghuan","1236");

        //登录,如果登录失败会报错
        subject.login(token);
}
```

2）加盐的md5

```java
package com.koal;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.ByteSource;

public class Md5Realm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        String principal = (String)authenticationToken.getPrincipal();

        if("zhanghuan".equals(principal)){
            //身份信息、 md5+salt之后的密码、 salt、 realm名字
            return new SimpleAuthenticationInfo(principal,
                    "88316675d7882e3fdbe066000273842c",
                    ByteSource.Util.bytes("aaa"),
                    this.getName());
        }
        return null;
    }
}

```

测试类一样（密码123456），不再赘述

3）加盐并散列2048次

```java
package com.koal;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.ByteSource;

public class Md5Realm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        String principal = (String)authenticationToken.getPrincipal();

        if("zhanghuan".equals(principal)){
            //身份信息、 md5+salt之后2048次散列的密码、 salt、 realm名字
            return new SimpleAuthenticationInfo(principal,
                    "3903fbfa95d94188502449ae5d16460f",
                    ByteSource.Util.bytes("aaaa"),
                    this.getName());
        }
        return null;
    }
}
```

```java
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;

@org.junit.Test
public class Test {

        //创建安全管理器对象
        SecurityManager securityManager = new DefaultSecurityManager();

        //创建realm
        Md5Realm md5Realm = new Md5Realm();
        //使用hash凭证匹配器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        credentialsMatcher.setHashAlgorithmName("md5");
    	credentialsMatcher.setHashIterations(2048);
        md5Realm.setCredentialsMatcher(credentialsMatcher);

        //给安全管理器设置realm
        ((DefaultSecurityManager)securityManager).setRealm(md5Realm);

        //给安全管理工具设置安全管理类
        SecurityUtils.setSecurityManager(securityManager);

        //获取subject主体
        Subject subject = SecurityUtils.getSubject();

        //创建token
        UsernamePasswordToken token = new UsernamePasswordToken("zhanghuan","1236");

        //登录,如果登录失败会报错
        subject.login(token);
}
```

# 权限字符串

规则是：`资源标识符:操作:资源实例标识符`，意思是对那个资源的哪个实例有什么操作

例子：

* 用户创建权限：user:create或user:crete:*
* 用户修改实例001的权限：user:update:001
* 用户实例001的所有权限：user:*;001



# 自定义realm之授权

```java
package com.koal;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.ByteSource;

public class Md5Realm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        String primaryPrincipal = (String) principalCollection.getPrimaryPrincipal();
        System.out.println("身份信息："+primaryPrincipal);
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        simpleAuthorizationInfo.addRole("admin");
        simpleAuthorizationInfo.addStringPermission("user:*:001");
        return simpleAuthorizationInfo;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        String principal = (String)authenticationToken.getPrincipal();

        if("zhanghuan".equals(principal)){
            //身份信息、 md5+salt之后的密码、 salt、 realm名字
            return new SimpleAuthenticationInfo(principal,
                    "3903fbfa95d94188502449ae5d16460f",
                    ByteSource.Util.bytes("aaaa"),
                    this.getName());
        }
        return null;
    }
}

```

```java
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;

@org.junit.Test
public class Test {

        //创建安全管理器对象
        SecurityManager securityManager = new DefaultSecurityManager();

        //创建realm
        Md5Realm md5Realm = new Md5Realm();
        //使用hash凭证匹配器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        credentialsMatcher.setHashAlgorithmName("md5");
    	credentialsMatcher.setHashIterations(2048);
        md5Realm.setCredentialsMatcher(credentialsMatcher);

        //给安全管理器设置realm
        ((DefaultSecurityManager)securityManager).setRealm(md5Realm);

        //给安全管理工具设置安全管理类
        SecurityUtils.setSecurityManager(securityManager);

        //获取subject主体
        Subject subject = SecurityUtils.getSubject();

        //创建token
        UsernamePasswordToken token = new UsernamePasswordToken("zhanghuan","1236");

        //登录,如果登录失败会报错
        subject.login(token);
    
    	if(subject.isAuthenticated()){
            System.out.println("role:"+subject.hasRole("admin"));
            System.out.println("permition:"+subject.isPermitted("user:*:001"));
        }
}
```



# shiro中授权实现方式

1）编程式

```java
Subject subject = SecurityUtils,getSubject();
if(subject,hasRole("admin")){
    //有权限
}else{
    //无权限
}
```

2）注解式

```java
@RquireePoles("admin")
public void Hello(){
    
}
```

3）标签式

```jsp
<shiro :hasRole name="admin">
	<!--有权限-->
</shiro :hasRole>
```



# springboot中整合shiro

1）依赖

```xml
		<dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.5.3</version>
        </dependency>
        <dependency>
            <groupId>commons-lang</groupId>
            <artifactId>commons-lang</artifactId>
            <version>2.6</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpcore</artifactId>
            <version>4.4.5</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.4</version>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.8.5</version>
        </dependency>
```

2）工具类

```java
package com.example.demo.utils;

import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;

public class HttpContextUtils {

    public static HttpServletRequest getHttpServletRequest() {
        return ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();
    }

    public static String getDomain(){
        HttpServletRequest request = getHttpServletRequest();
        StringBuffer url = request.getRequestURL();
        return url.delete(url.length() - request.getRequestURI().length(), url.length()).toString();
    }

    public static String getOrigin(){
        HttpServletRequest request = getHttpServletRequest();
        return request.getHeader("Origin");
    }
}
```

```java
package com.example.demo.utils;

import org.apache.http.HttpStatus;

import java.util.HashMap;
import java.util.Map;

/**
 * 返回数据
 *
 * @author Mark sunlightcs@gmail.com
 */
public class R extends HashMap<String, Object> {
    private static final long serialVersionUID = 1L;

    public R() {
        put("code", 0);
        put("msg", "success");
    }

    public static R error() {
        return error(HttpStatus.SC_INTERNAL_SERVER_ERROR, "未知异常，请联系管理员");
    }

    public static R error(String msg) {
        return error(HttpStatus.SC_INTERNAL_SERVER_ERROR, msg);
    }

    public static R error(int code, String msg) {
        R r = new R();
        r.put("code", code);
        r.put("msg", msg);
        return r;
    }

    public static R ok(String msg) {
        R r = new R();
        r.put("msg", msg);
        return r;
    }

    public static R ok(Map<String, Object> map) {
        R r = new R();
        r.putAll(map);
        return r;
    }

    public static R ok() {
        return new R();
    }

    @Override
    public R put(String key, Object value) {
        super.put(key, value);
        return this;
    }
}

```



3）实体类

```java
package com.example.demo.entity;

import lombok.Data;

import java.io.Serializable;
import java.util.Date;
import java.util.List;

@Data
public class SysUserEntity implements Serializable {
    private static final long serialVersionUID = 1L;

    /**
     * 用户ID
     */
    private String userId;

    /**
     * 用户名
     */
    private String username;

    /**
     * 密码
     */
    private String password;

    /**
     * 盐
     */
    private String salt;

    /**
     * 邮箱
     */
    private String email;

    /**
     * 手机号
     */
    private String mobile;

    /**
     * 状态  0：禁用   1：正常
     */
    private Integer status;

    /**
     * 角色ID列表
     */
    private List<String> roleIdList;

    /**
     * 创建者ID
     */
    private String createUserId;

    /**
     * 创建时间
     */
    private Date createTime;

}

```

```java
package com.example.demo.entity;

import lombok.Data;

import java.io.Serializable;
import java.util.Date;

@Data
public class SysUserTokenEntity implements Serializable {
    private static final long serialVersionUID = 1L;

    //用户ID
    private String userId;
    //token
    private String token;
    //过期时间
    private Date expireTime;
    //更新时间
    private Date updateTime;

}

```



4）配置

```java
package com.example.demo.config;

import org.apache.shiro.authc.AuthenticationToken;

/**
 * token
 *
 * @author Mark sunlightcs@gmail.com
 */
public class OAuth2Token implements AuthenticationToken {
    private String token;

    public OAuth2Token(String token){
        this.token = token;
    }

    @Override
    public String getPrincipal() {
        return token;
    }

    @Override
    public Object getCredentials() {
        return token;
    }
}
```

```java
package com.example.demo.config;

import com.example.demo.utils.HttpContextUtils;
import com.example.demo.utils.R;
import com.google.gson.Gson;
import org.apache.commons.lang.StringUtils;
import org.apache.http.HttpStatus;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.web.filter.authc.AuthenticatingFilter;
import org.springframework.web.bind.annotation.RequestMethod;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * oauth2过滤器，从请求中获取token
 *
 * @author Mark sunlightcs@gmail.com
 */
public class OAuth2Filter extends AuthenticatingFilter {

    @Override
    protected AuthenticationToken createToken(ServletRequest request, ServletResponse response) throws Exception {
        //获取请求token
        String token = getRequestToken((HttpServletRequest) request);

        if(StringUtils.isBlank(token)){
            return null;
        }

        return new OAuth2Token(token);
    }

    @Override
    protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) {
        if(((HttpServletRequest) request).getMethod().equals(RequestMethod.OPTIONS.name())){
            return true;
        }

        return false;
    }

    @Override
    protected boolean onAccessDenied(ServletRequest request, ServletResponse response) throws Exception {
        //获取请求token，如果token不存在，直接返回401
        String token = getRequestToken((HttpServletRequest) request);
        if(StringUtils.isBlank(token)){
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            httpResponse.setHeader("Access-Control-Allow-Credentials", "true");
            httpResponse.setHeader("Access-Control-Allow-Origin", HttpContextUtils.getOrigin());

            String json = new Gson().toJson(R.error(HttpStatus.SC_UNAUTHORIZED, "invalid token"));

            httpResponse.getWriter().print(json);

            return false;
        }

        return executeLogin(request, response);
    }

    @Override
    protected boolean onLoginFailure(AuthenticationToken token, AuthenticationException e, ServletRequest request, ServletResponse response) {
        HttpServletResponse httpResponse = (HttpServletResponse) response;
        httpResponse.setContentType("application/json;charset=utf-8");
        httpResponse.setHeader("Access-Control-Allow-Credentials", "true");
        httpResponse.setHeader("Access-Control-Allow-Origin", HttpContextUtils.getOrigin());
        try {
            //处理登录失败的异常
            Throwable throwable = e.getCause() == null ? e : e.getCause();
            R r = R.error(HttpStatus.SC_UNAUTHORIZED, throwable.getMessage());

            String json = new Gson().toJson(r);
            httpResponse.getWriter().print(json);
        } catch (IOException e1) {

        }

        return false;
    }

    /**
     * 获取请求的token
     */
    private String getRequestToken(HttpServletRequest httpRequest){
        //从header中获取token
        String token = httpRequest.getHeader("token");

        //如果header中不存在token，则从参数中获取token
        if(StringUtils.isBlank(token)){
            token = httpRequest.getParameter("token");
        }

        return token;
    }


}
```

```java
package com.example.demo.config;

import com.example.demo.entity.SysUserEntity;
import com.example.demo.entity.SysUserTokenEntity;
import org.apache.commons.lang.time.DateUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.HashSet;
import java.util.Set;

/**
 * 认证
 *
 * @author Mark sunlightcs@gmail.com
 */
@Component
public class OAuth2Realm extends AuthorizingRealm {

    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof OAuth2Token;
    }

    /**
     * 授权(验证权限时调用)
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        SysUserEntity user = (SysUserEntity)principals.getPrimaryPrincipal();
        String userId = user.getUserId();

        //用户权限列表
        Set<String> permsSet = new HashSet<>();
        permsSet.add("sys:log:list");

        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        info.setStringPermissions(permsSet);
        return info;
    }

    /**
     * 认证(登录时调用)
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        String accessToken = (String) token.getPrincipal();

        //根据accessToken，查询用户信息，检查token是否失效
        SysUserTokenEntity tokenEntity = new SysUserTokenEntity();
        tokenEntity.setToken(accessToken);
        Date date = DateUtils.addDays(new Date(), 1);
        tokenEntity.setExpireTime(date);
        //token失效
        if(tokenEntity == null || tokenEntity.getExpireTime().getTime() < System.currentTimeMillis()){
            throw new IncorrectCredentialsException("token失效，请重新登录");
        }

        //查询用户信息，检查用户是否被锁定
        SysUserEntity user = new SysUserEntity();
        user.setUserId("1");
        user.setStatus(1);
        //账号锁定
        if(user.getStatus() == 0){
            throw new LockedAccountException("账号已被锁定,请联系管理员");
        }

        SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(user, accessToken, getName());
        return info;
    }
}

```

```java
package com.example.demo.config;

import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.spring.LifecycleBeanPostProcessor;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.DependsOn;

import javax.servlet.Filter;
import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;

/**
 * Shiro配置
 *
 * @author Mark sunlightcs@gmail.com
 */
@Configuration
public class ShiroConfig {

    @Bean("securityManager")
    public SecurityManager securityManager(OAuth2Realm oAuth2Realm) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(oAuth2Realm);
        securityManager.setRememberMeManager(null);
        return securityManager;
    }

    @Bean("shiroFilter")
    public ShiroFilterFactoryBean shiroFilter(SecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilter = new ShiroFilterFactoryBean();
        shiroFilter.setSecurityManager(securityManager);

        //设置自定义oauth过滤
        Map<String, Filter> filters = new HashMap<>();
        filters.put("oauth2", new OAuth2Filter());
        shiroFilter.setFilters(filters);

        Map<String, String> filterMap = new LinkedHashMap<>();
        filterMap.put("/webjars/**", "anon");
        filterMap.put("/druid/**", "anon");
        filterMap.put("/app/**", "anon");
        filterMap.put("/sys/login", "anon");
        filterMap.put("/swagger/**", "anon");
        filterMap.put("/v2/api-docs", "anon");
        filterMap.put("/swagger-ui.html", "anon");
        filterMap.put("/swagger-resources/**", "anon");
        filterMap.put("/captcha.jpg", "anon");
        filterMap.put("/aaa.txt", "anon");
        filterMap.put("/**", "oauth2");
        shiroFilter.setFilterChainDefinitionMap(filterMap);

        return shiroFilter;
    }

    @Bean("lifecycleBeanPostProcessor")
    public LifecycleBeanPostProcessor lifecycleBeanPostProcessor() {
        return new LifecycleBeanPostProcessor();
    }

    @Bean
    @DependsOn({"lifecycleBeanPostProcessor"})
    public DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        advisorAutoProxyCreator.setProxyTargetClass(true);
        return advisorAutoProxyCreator;
    }

    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(SecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor advisor = new AuthorizationAttributeSourceAdvisor();
        advisor.setSecurityManager(securityManager);
        return advisor;
    }

}
```



5）测试控制器

```java
package com.example.demo.controller;

import org.apache.shiro.authz.annotation.RequiresPermissions;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;


@RestController
public class TestController {

    @GetMapping("test")
    @RequiresPermissions("sys:log:list")
    public String test(){
        return "test";
    }
}

```




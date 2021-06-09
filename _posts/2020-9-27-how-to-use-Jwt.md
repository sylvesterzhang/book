---
layout: post
title: Jwt
tags: Jwt
categories: backends
---

概述、使用



**概述**

官方文档是这样解释的：JSON Web Token（JWT）是一个开放标准（RFC 7519），它定义了一种紧凑且独立的方式，可以在各方之间作为JSON对象安全地传输信息。此信息可以通过数字签名进行验证和信任。JWT可以使用秘密（使用HMAC算法）或使用RSA或ECDSA的公钥/私钥对进行签名。 

`1`使用场景

 授权：这是最常见的使用场景，解决单点登录问题。 

 信息加密：将传输的信息进行加密。 

`2`数据结构

1）HEADER

包含 token的类型（“JWT”）和算法名称（比如：HMAC SHA256或者RSA等等） 

2）PAYLOAD

数据信息

3） VERIFY SIGNATURE 

签证信息

---

**使用**

`1`依赖

```xml
<!-- https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>

```

`2`代码

```java
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;

//部分代码
    @Test
    public void test6(){
        //创建键值对
        HashMap<String,Object> map=new HashMap<>();
        map.put("hobby","football");
        
        //创建加密字符串
        JwtBuilder builder= Jwts.builder()
                .setId("888")   //设置唯一编号
                .setSubject("主题")//设置主题,可以是JSON数据
                .setIssuedAt(new Date())//设置签发日期
                .addClaims(map)//新增键值对
                .setIssuer("签发者")//设置签发者
                .setExpiration(new Date(
                    System.currentTimeMillis()+60*1000
                ))//设置过期时间，过期后解析会抛出错误
                .signWith(SignatureAlgorithm.HS256,"123456");//设置签名 使用HS256算法，并设置SecretKey(字符串)
        String token=builder.compact();
        System.out.println( token );

        //解析加密字符串
        Claims claims = Jwts.parser().setSigningKey("123456").parseClaimsJws(token).getBody();
        System.out.println(claims);
    }
```

结果

```
eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4ODgiLCJzdWIiOiLkuLvpopgiLCJpYXQiOjE2MDExODc4ODIsImhvYmJ5IjoiZm9vdGJhbGwiLCJpc3MiOiLnrb7lj5HogIUiLCJleHAiOjE2MDExODc5NDJ9.qnnWCnoxFCPFkRQPB9Ie9vFDZpIuzVHsGlygIouFVy0
{jti=888, sub=主题, iat=1601187882, hobby=football, iss=签发者, exp=1601187942}

```


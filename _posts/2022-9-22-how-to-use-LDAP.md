---
layout: post
title: LDAP总结
tags: database
categories: database
---

目录服务、目录书概念、LDAP、docker-compose方式安装、springboot中访问

{{toC}}

# 目录服务

1）目录服务是一个特殊的数据库.用来保存描述性的、基于属性的详细信息，支持过滤功能。
2）是动态的，灵活的，易扩展的。
如：人员组织管理，电话箱，地址簿。

# 目录树概念
1）目录树：在一个目录服务系统中，整个目录信息集可以表示为一个目录信息树，树中的每个节点是一个条目。
2）条目：每个条目就是一条记录，每个条目有自己的唯一可区别的名称(DN)。
3）对象类：与某个实体类型对应的一组屈性，对象类是可以继承的，这样父类的必须属性也会被继承下来。
4.属性：描述条目的某个方面的信息。一个属性由一个属性类型和一个或多个属性值组成，属性有必须属性和非必须属性。

# LDAP

LDAP (Light Directory Access Portocol),它是基于X.50O标准的轻录级目录访问协议.
目录是一个为查询、测览和搜索而优化的数据库，它成树状结构组织数据，类以文件目录一样。
目录数据库和关系数据库不同，它有优异的读性能.但写性能差，并且没有事务处理、回滚等复杂功能，不适于存储修改频繁的数据。所以目录天生是用来查询的就好象它的名字一样。
LDAP目录服务是由目录数据库和一套访问协议组成的系统。



| 关键词 | 英文简称           | 含义                                                         |
| ------ | ------------------ | ------------------------------------------------------------ |
| uid    | User ld            | 用户ID songtao.xu(一条记录的lD)                              |
| ou     | Organization Unit  | 组织单位，组织单位可以包含其他各种对象（包括其他组织单元）   |
| cn     | Common Name        | 公共名称，如Thomas Johansson(一条记录的名称)                 |
| sn     | Surname            | 姓，如“许”                                                   |
| dn     | Distinguished Name | “uid=songtao.xu,ou=oa，dc=example,dc=com",一条记录的位置（唯一）相对辨别名，类似于文件系统中的相对路径，它是与目录树结构无关的部分， |

# docker-compose方式安装

```yml
version: "3"
services:
  # 1.安装openldap
  openldap:
    container_name: openldap
    image: osixia/openldap:1.4.0
    restart: always
    environment:
      LDAP_ORGANISATION: "test openldap"
      LDAP_DOMAIN: "koal.com"
      LDAP_ADMIN_PASSWORD: "12345678"
      LDAP_CONFIG_PASSWORD: "12345678"
    volumes:
      - /data/openldap/data:/var/lib/ldap
      - /data/openldap/config:/etc/ldap/slapd.d
    ports:
      - '389:389'
  # 2.安装phpldapadmin
  phpldapadmin:
    container_name: phpldapadmin
    image: osixia/phpldapadmin:latest
    restart: always
    environment:
      PHPLDAPADMIN_HTTPS: "false"
      PHPLDAPADMIN_LDAP_HOSTS: openldap
    ports:
      - '30004:80'
    depends_on:
      - openldap
      # cn=admin,dc=test,dc=com 密码：123456
```

# springboot中访问

1）依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-ldap</artifactId>
</dependency>
```

2）配置

这里从数据库中取，也可以从配置变量中取

```java
import cn.hutool.json.JSONObject;
import com.koal.modules.sys.service.SysConfigService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.ldap.core.LdapTemplate;
import org.springframework.ldap.core.support.LdapContextSource;

import java.util.HashMap;
import java.util.Map;

/**
 * LDAP 的自动配置类
 *
 * 完成连接 及LdapTemplate生成
 */
@Configuration
public class LdapConfiguration {

    @Autowired
    SysConfigService sysConfigService;

    @Bean
    public LdapContextSource getContextSource() {
        LdapContextSource contextSource = new LdapContextSource();
        Map<String, Object> config = new HashMap();

        String ldapConfString = sysConfigService.getValue("SYS_NORMAL_CONFIG_KEY");
        JSONObject ldapConf = new JSONObject(ldapConfString);

        contextSource.setUrl((String) ldapConf.getByPath("ldap.urls"));
        contextSource.setBase((String) ldapConf.getByPath("ldap.base"));
        contextSource.setUserDn( (String) ldapConf.getByPath("ldap.username"));
        contextSource.setPassword((String) ldapConf.getByPath("ldap.password"));

        //  解决 乱码 的关键一句
        config.put("java.naming.ldap.attributes.binary", "objectGUID");

        contextSource.setPooled(true);
        contextSource.setBaseEnvironmentProperties(config);
        return contextSource;
    }


    @Bean
    public LdapTemplate getLdapTemplate() {
        return new LdapTemplate(getContextSource());
    }

}
```

3）自定义实体类

```java

import com.fasterxml.jackson.annotation.JsonIgnore;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.ldap.odm.annotations.Attribute;
import org.springframework.ldap.odm.annotations.Entry;
import org.springframework.ldap.odm.annotations.Id;

import javax.naming.Name;
import java.util.List;

/**
 * @author sunjinchen
 */
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@Entry(objectClasses = {"inetOrgPerson", "userSecurityInformation", "person"})
public class UserLdap {
    @Id
    @JsonIgnore
    private Name dn;
    /**
     * uid
     */
    private String uid;
    /**
     * 名
     */
    private String givenName;
    /**
     * 姓
     */
    private String sn;
    /**
     * 地区
     */
    private String street;
    /**
     * 部门
     */
    private String ou;
    /**
     * 组名（多个组，需要创建多个employeeType属性）
     */
    @Attribute(name="employeeType")
    private List<String> employeeType;

    @Attribute(name="objectClass")
    private List<String> objectClass;
    /**
     * 登录名
     */
    @Attribute(name="displayName")
    private String displayName;
    /**
     * 电子邮件
     */
    @Attribute(name="mail")
    private String mail;
    /**
     * 姓名
     */
    private String cn;
    /**
     * 状态 2000 - 正常 -1000 停用
     */
    private String st;
    /**
     * 岗位（编号）
     */
    private String businessCategory;
}

```

4）操作

```java
package com.koal;

import com.koal.modules.ldap.entity.UserLdap;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.ldap.core.LdapTemplate;
import org.springframework.ldap.core.NameAwareAttribute;
import org.springframework.ldap.query.LdapQueryBuilder;
import org.springframework.test.context.junit4.SpringRunner;

import javax.naming.InvalidNameException;
import javax.naming.Name;
import javax.naming.directory.DirContext;
import javax.naming.directory.ModificationItem;
import javax.naming.ldap.LdapName;
import java.util.ArrayList;
import java.util.List;

@RunWith(SpringRunner.class)
@SpringBootTest
public class Test {
    @Autowired
    LdapTemplate ldapTemplate;

    /**
     * 查询
     */
    @org.junit.Test
    public void test1(){
        List<UserLdap> list= ldapTemplate.find(LdapQueryBuilder.query().where("displayName").like("zhanghuan"), UserLdap.class);
        System.out.println(list);
    }

    /**
     * 修改
     * @throws InvalidNameException
     */
    @org.junit.Test
    public void test2() throws InvalidNameException {
        Name name = new LdapName("cn=redmine, ou=KMISTR");
        ModificationItem[] modificationItems = new ModificationItem[1];
        NameAwareAttribute nameAwareAttribute = new NameAwareAttribute("employeeType");
        nameAwareAttribute.add("test");
        nameAwareAttribute.add("test1");
        modificationItems[0] = new ModificationItem(DirContext.REPLACE_ATTRIBUTE,
                nameAwareAttribute);

        ldapTemplate.modifyAttributes(name,modificationItems);
    }

    /**
     * 修改
     */
    @org.junit.Test
    public void test3(){
        List<UserLdap> list= ldapTemplate.find(LdapQueryBuilder.query().where("cn").is("redmine"),UserLdap.class);
        System.out.println(list);
        if(list.size()>0){
            UserLdap userLdap = list.get(0);
            List<String> employeeType = new ArrayList<>();
            employeeType.add("test000");
            userLdap.setEmployeeType(employeeType);
            ldapTemplate.update(userLdap);
        }
    }

    /**
     * 新增
     * @throws InvalidNameException
     */
    @org.junit.Test
    public void test4() throws InvalidNameException {
        UserLdap userLdap = new UserLdap();
        Name dn = new LdapName("cn=test,ou=KMISTR");
        userLdap.setSn("test");
        userLdap.setDn(dn);
        userLdap.setOu("KMISTR");
        ldapTemplate.create(userLdap);
    }

    /**
     * 删除
     * @throws InvalidNameException
     */
    @org.junit.Test
    public void test5() throws InvalidNameException {
        Name dn = new LdapName("cn=test,ou=KMISTR");
        UserLdap userLdap = ldapTemplate.findByDn(dn,UserLdap.class);
        ldapTemplate.delete(userLdap);
    }
}

```


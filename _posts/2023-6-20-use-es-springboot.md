---
layout: post
title: springboot中使用es
tags: backend
categories: backend
---

依赖、配置、定义索引对象、操作、其他

# 依赖

```
<!-- Maven -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>

```

# 配置

```
spring:
  elasticsearch:
    rest:
      uris: http://localhost:9200
```

# 定义索引对象

```
package vip.xiaonuo;


import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;
import org.springframework.data.elasticsearch.annotations.FieldType;

@Data
@Document(indexName = "sysmenu")
public class SysMenuEs {

    @Id
    private String id;

    /** 父id */
    @Field(store = true, type = FieldType.Keyword)
    private String parentId;

    /** 标题 */
    @Field(store = true, type = FieldType.Keyword)
    private String title;

    /** 别名 */
    @Field(store = true, type = FieldType.Keyword)
    private String name;

    /** 编码 */
    @Field(store = true, type = FieldType.Keyword)
    private String code;

    /** 分类 */
    @Field(store = true, type = FieldType.Keyword)
    private String category;

    /** 模块 */
    @Field(store = true, type = FieldType.Keyword)
    private String module;

    /** 菜单类型 */
    @Field(store = true, type = FieldType.Keyword)
    private String menuType;

    /** 路径 */
    @Field(store = true, type = FieldType.Keyword)
    private String path;

    /** 组件 */
    @Field(store = true, type = FieldType.Keyword)
    private String component;

    /** 图标 */
    @Field(store = true, type = FieldType.Keyword)
    private String icon;

    /** 颜色 */
    @Field(store = true, type = FieldType.Keyword)
    private String color;

    /** 是否可见 */
    @Field(store = true, type = FieldType.Keyword)
    private String visible;

    /** 排序码 */
    @Field(store = true, type = FieldType.Integer)
    private Integer sortCode;

    /** 扩展信息 */
    @Field(store = true, type = FieldType.Keyword)
    private String extJson;
}

```

# 定义mapper

```
package vip.xiaonuo.mapper;

import org.springframework.data.elasticsearch.repository.ElasticsearchRepository;
import vip.xiaonuo.entitiy.SysMenuEs;

public interface SysMenuEsMapper extends ElasticsearchRepository<SysMenuEs,String> {
}

```

# 操作

```
package vip.xiaonuo;

import cn.hutool.core.bean.BeanUtil;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.data.elasticsearch.core.ElasticsearchOperations;
import org.springframework.data.elasticsearch.core.SearchHit;
import org.springframework.data.elasticsearch.core.SearchHits;
import org.springframework.data.elasticsearch.core.query.Criteria;
import org.springframework.data.elasticsearch.core.query.CriteriaQuery;
import org.springframework.data.elasticsearch.core.query.Query;
import org.springframework.test.context.junit4.SpringRunner;
import vip.xiaonuo.entitiy.SysMenuEsMapper;
import vip.xiaonuo.sys.modular.resource.entity.SysMenu;
import vip.xiaonuo.sys.modular.resource.service.SysMenuService;

import java.util.List;
import java.util.stream.Collectors;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
public class MainTest {

    @Autowired
    SysMenuService sysMenuService;

    @Autowired
    SysMenuEsMapper sysMenuEsMapper;

    @Autowired
    private ElasticsearchOperations elasticsearchOperations;

    @Test
    public void test() {
        List<SysMenu> list = sysMenuService.list();
        for (SysMenu sysMenu : list) {
            SysMenuEs sysMenuEs = new SysMenuEs();
            BeanUtil.copyProperties(sysMenu,sysMenuEs);

            //新增
            sysMenuEsMapper.save(sysMenuEs);
        }
    }


    @Test
    public void test1(){
        //查询

        Sort sort = Sort.by("sortCode").ascending();
        Pageable pageable = PageRequest.of(1,10,sort);

        Criteria criteria = new Criteria("title").contains("管理")
                .and("sortCode").lessThan(60);

        Query query = new CriteriaQuery(criteria,pageable);
        SearchHits<SysMenuEs> searchHits = elasticsearchOperations.search(query, SysMenuEs.class);
        List<SearchHit<SysMenuEs>> searchHitList = searchHits.getSearchHits();
        List<SysMenuEs> sysMenuEsList = searchHitList.stream().map(SearchHit::getContent).collect(Collectors.toList());
        System.out.println(sysMenuEsList);
    }
}

```

# 其他 

```
在 Elasticsearch 中，fuzzy、matches 和 contains 是不同的查询操作符，它们用于在搜索中进行模糊匹配和字符串查询。

Fuzzy（模糊匹配）： fuzzy() 方法用于在查询时进行模糊匹配，即在搜索时考虑词项的拼写错误。该方法接受一个 String 参数作为搜索词项，并默认进行最大编辑距离为2的模糊匹配查询。

例如：criteria.fuzzy("fieldName").value("apple").boost(1.0f) 将会在 "fieldName" 字段内搜索与 "apple" 类似的词项，允许最大编辑距离为 2，同时可以通过 boost() 方法设置查询的权重。

Matches（匹配）： matches() 方法用于进行全文匹配查询，可以匹配词项的全文内容。该方法接受一个 String 参数作为搜索词项，并会对该词项进行全文匹配。

例如：criteria.matches("fieldName").value("apple").boost(1.0f) 将会在 "fieldName" 字段内搜索与 "apple" 完全匹配的词项，并可以通过 boost() 方法设置查询的权重。

Contains（包含）： contains() 方法用于检查字段是否包含指定的子字符串。该方法接受一个 String 参数作为子字符串，并会在字段中进行部分匹配。

例如：criteria.contains("fieldName").value("apple").boost(1.0f) 将会在 "fieldName" 字段内搜索包含子字符串 "apple" 的词项，并可以通过 boost() 方法设置查询的权重。

总结来说，fuzzy 是一种模糊匹配的查询方式，允许拼写错误，而 matches 和 contains 则是对词项进行全文或部分匹配的查询方式。你可以根据具体的查询需求和数据特点选择合适的查询操作符。
```


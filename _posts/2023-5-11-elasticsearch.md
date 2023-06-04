---
layout: post
title: elasticsearch使用
tags: backend
categories: backend
---





索引操作、数据操作



# 索引操作

## 创建索引

```
PUT /shopping
```

```
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "shopping"
}
```

## 获取索引详细信息

```
GET /_cat/indices?v
```

```
health status index                           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   shopping                        Hv0juGrISIaJ__RszyAHJA   1   1          0            0       283b           283b
```

## 删除索引

```
DELETE /shopping
```



# 数据操作

## 添加数据

```
POST /shopping/_doc
{
	"title":"小米手机",
	"category":"小米",
	"price":3999.00
}
```

指定id添加数据

```
POST /shopping/_doc/100
{
	"title":"小米手机",
	"category":"小米",
	"price":3999.00
}

PUT /shopping/_doc/101
{
	"title":"小米手机",
	"category":"小米",
	"price":3999.00
}

POST /shopping/_create/102
{
	"title":"小米手机",
	"category":"小米",
	"price":3999.00
}
```

## 查询1条数据

```
GET /shopping/_doc/102
```

## 查看所有数据

```
GET /shopping/search
```

## 局部更新数据

```
POST /shopping/_update/102
{
	"doc":{
		"title":"华为手机"
	}
}
```

## 删除1条数据

```
DELETE /shopping/_doc/102
```

## 条件查询

1）指定字段分页查询并排序

```
GET /shopping/_search
{"query": {
    "match": {
        "title":"小米"
    }
 },
 "_source": "title", 
 "from": 0,
 "size":1,
 "sort": [
   {
     "price": {
       "order": "desc"
     }
   }
 ]
}
```

```
{
  "took" : 17,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 3,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [
      {
        "_index" : "shopping",
        "_type" : "_doc",
        "_id" : "qLmnhYgBDnKcjmGSyRGG",
        "_score" : null,
        "_source" : {
          "title" : "小米手机"
        },
        "sort" : [
          3999.0
        ]
      }
    ]
  }
}
```

2）多条件查询

```
GET /shopping/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "category": "小米"
          }
        },
        {
          "match": {
            "price": 3999
          }
        }
      ],
      "filter": {
        "range": {
          "price": {
            "gt": 1000
          }
        }
      }
    }
  }
}
```

must相当于and，should相当于or

```
{
  "took" : 13,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 4,
      "relation" : "eq"
    },
    "max_score" : 1.1740228,
    "hits" : [
      {
        "_index" : "shopping",
        "_type" : "_doc",
        "_id" : "qLmnhYgBDnKcjmGSyRGG",
        "_score" : 1.1740228,
        "_source" : {
          "title" : "小米手机",
          "category" : "小米",
          "price" : 3999.0
        }
      },
      {
        "_index" : "shopping",
        "_type" : "_doc",
        "_id" : "100",
        "_score" : 1.1740228,
        "_source" : {
          "title" : "小米手机",
          "category" : "小米",
          "price" : 3999.0
        }
      },
      {
        "_index" : "shopping",
        "_type" : "_doc",
        "_id" : "101",
        "_score" : 1.1740228,
        "_source" : {
          "title" : "小米手机",
          "category" : "小米",
          "price" : 3999.0
        }
      },
      {
        "_index" : "shopping",
        "_type" : "_doc",
        "_id" : "102",
        "_score" : 1.1740228,
        "_source" : {
          "title" : "华为手机",
          "category" : "小米",
          "price" : 3999.0
        }
      }
    ]
  }
}

```

3）完全匹配

```
GET /shopping/_search
{
  "query": {
    "match_phrase": {
      "title": "小米"
    }
  }
}
```


---
layout: post
title: logstash和filebeat使用
tags: backend
categories: backend
---

应用场景、组件介绍、logstash启动、filebeat启动



# 应用场景

分布式场景中，不同服务器的服务日志集中收集管理，方便排查问题

# 组件介绍

## logstash

日志收集器，将接受到的日志存储到ES中

## fielbeat

日志解析器，将日志解析后通过网络发送给日志收集器

# logstash启动

## 下载

```
https://www.elastic.co/cn/downloads/past-releases/logstash-7-17-15
```

## 修改配置文件

编辑logstash.conf

```
input {
  beats {
    port => 5044
  }
}
filter {
      mutate {
        rename => { "@tags" => "channel" }
      }
        ruby {
                code => "event.set('timestamp', event.get('@timestamp').time.localtime + 8*60*60)"
        }
        ruby {
                code => "event.set('@timestamp',event.get('timestamp'))"
        }
        mutate {
                remove_field => ["timestamp"]
        }
 
    }
output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
    #user => "elastic"
    #password => "changeme"
  }
}
```

## 启动

```
./bin/logstash -f ./config/logstash.conf
```

# filebeat启动

## 下载

```
https://www.elastic.co/cn/downloads/past-releases/filebeat-7-17-15
```

## 修改配置文件

修改filebeat.yml

```
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /opt/javaService/filebeat-7.17.15-linux-x86_64/logs/*.log
  fields:
    type: test
  fields_under_root: true


output.logstash:
  hosts: ["localhost:5044"]
```

配置项含义
```
filebeat.inputs：Filebeat输入配置的起始标记。

type: log：指定输入类型为日志文件类型，这告诉Filebeat将读取指定路径下的日志文件。

tail_files: true：设置为true，使得Filebeat在读取日志文件时跟随文件尾部的新内容。

backoff: "1s"：在出现错误时重试读取文件的时间间隔。

paths：设置要监控的日志文件路径。示例中的配置分别指定了/var/logs/applog/*.txt和/var/logs/appjson/*.json路径下的文件。

fields：为读取的每个事件添加自定义字段，这里设置了一个名为"type"的字段，并为每个输入类型分别设置为"apptxt"和"appjson"。

fields_under_root: true：将所有自定义字段放置在根级别而非在fields下。这样做可以使得自定义字段在Elasticsearch中更容易进行检索和处理。

multiline.pattern: '^[[:space:]]'：设置正则表达式模式，用于匹配日志文件中新的一行。这里的模式是以空白字符开头的行。

multiline.negate: false：设置是否对匹配的行进行取反操作。在这里，设置为false，意味着只有匹配模式的行才会进行合并。

multiline.match: after：指定多行日志合并的方式。在这里，设置为"after"，表示合并匹配行之后的内容。也就是说，将匹配行的下一行以及后续的行与该行合并为一条完整的事件。

通过以上配置，Filebeat将监控指定路径下的.txt和.json文件，并读取它们的内容。每读取一行日志，Filebeat会将其发送到指定的输出（可在配置文件中定义），并添加自定义字段以供后续处理和索引。
```

## 启动

```
./filebeat -e -c filebeat.yml
```
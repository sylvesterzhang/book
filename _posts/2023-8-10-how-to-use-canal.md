---
layout: post
title: canal使用
tags: backend
categories: backend
---

工作原理、mysql开启二进制日志、启动服务端、启动客户端


# 工作原理

- canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送dump 协议
- MySQL master 收到 dump 请求，开始推送 binary log 给 slave (即 canal )
- canal 解析 binary log 对象(原始为 byte 流)

# mysql开启二进制日志

## 1）定义docker-compose.yml文件

```
version: '3'
services:
  master:
    image: mysql:8.0.28
    container_name: mysql-master
    ports:
    - '33306:3306'
    restart: always
    hostname: mysql-master
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
      ADMIN_USER: "root"
      ADMIN_PASSWORD: "123456"
      TZ: "Asia/Shanghai"
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: 1
    healthcheck:
      test: ["CMD","mysqladmin","-uroot","-p$${MYSQL_ROOT_PASSWORD}","ping","-h","localhost"]
      timeout: 2s
      interval: 10s
      retries: 5
      start_period: 5s
    logging:
      options:
        max-file: '1'
        max-size: '128k'
    command:
    -  "--server-id=1"
    -  "--character-set-server=utf8mb4"
    -  "--collation-server=utf8mb4_unicode_ci"
    -  "--log-bin=mysql-bin"
    -  "--sync_binlog=1"
    -  "--binlog-ignore-db=mysql"
    -  "--binlog-ignore-db=sys"
    -  "--binlog-ignore-db=performance_schema"
    -  "--binlog-ignore-db=information_schema"
    -  "--sql_mode=NO_AUTO_VALUE_ON_ZERO,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION,PIPES_AS_CONCAT,ANSI_QUOTES"
    volumes:
    - ./data:/var/lib/mysql
```

## 2）查看状态

```
SHOW MASTER STATUS;
```

## 3）创建用户

```
CREATE USER 'repl'@'%' IDENTIFIED BY '123456';

GRANT ALL PRIVILEGES ON *.* TO 'repl'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;
```

> 其他知识点

> 主从复制需要在从库配置文件中写入以下配置
- MASTER_SYNC_USER: "root" #设置脚本中定义的用于同步的账号
- MASTER_SYNC_PASSWORD: "123456" #设置脚本中定义的用于同步的密码
- ALLOW_HOST: "10.10.%.%" #允许同步账号的host地址


# 启动canal服务端

## 1）下载并解压
```
https://github.com/alibaba/canal/releases/tag/canal-1.1.7
```

## 2）修改配置文件
```
vi conf/example/instance.properties
```

```
## mysql serverId
canal.instance.mysql.slaveId = 1234
#position info，需要改成自己的数据库信息
canal.instance.master.address = 127.0.0.1:3306 
canal.instance.master.journal.name = 
canal.instance.master.position = 
canal.instance.master.timestamp = 
#canal.instance.standby.address = 
#canal.instance.standby.journal.name =
#canal.instance.standby.position = 
#canal.instance.standby.timestamp = 
#username/password，需要改成自己的数据库信息
canal.instance.dbUsername = canal  
canal.instance.dbPassword = canal
canal.instance.defaultDatabaseName =
canal.instance.connectionCharset = UTF-8
#table regex
canal.instance.filter.regex = .\*\\\\..\*
```

## 3）启动

```
sh bin/startup.sh
```

# 启动客户端

## 1）引入依赖
```
<dependency>
	<groupId>com.alibaba.otter</groupId>
	<artifactId>canal.client</artifactId>
	<version>1.1.0</version>
</dependency>
```

## 2）客户端代码
```
package com.example.test;
import java.net.InetSocketAddress;
import java.util.List;


import com.alibaba.otter.canal.client.CanalConnectors;
import com.alibaba.otter.canal.client.CanalConnector;
import com.alibaba.otter.canal.common.utils.AddressUtils;
import com.alibaba.otter.canal.protocol.Message;
import com.alibaba.otter.canal.protocol.CanalEntry.Column;
import com.alibaba.otter.canal.protocol.CanalEntry.Entry;
import com.alibaba.otter.canal.protocol.CanalEntry.EntryType;
import com.alibaba.otter.canal.protocol.CanalEntry.EventType;
import com.alibaba.otter.canal.protocol.CanalEntry.RowChange;
import com.alibaba.otter.canal.protocol.CanalEntry.RowData;

public class SimpleCanalClientExample {

    public static void main(String args[]) {
        // 创建链接
        CanalConnector connector = CanalConnectors.newSingleConnector(new InetSocketAddress("192.168.0.215",
                11111), "example", "", "");
        int batchSize = 1000;

        try {
            connector.connect();
            connector.subscribe(".*\\..*");
            connector.rollback();
            while (true) {
                Message message = connector.getWithoutAck(batchSize); // 获取指定数量的数据
                long batchId = message.getId();
                int size = message.getEntries().size();
                if (batchId == -1 || size == 0) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                    }
                } else {
                    // System.out.printf("message[batchId=%s,size=%s] \n", batchId, size);
                    printEntry(message.getEntries());
                }

                connector.ack(batchId); // 提交确认
                // connector.rollback(batchId); // 处理失败, 回滚数据
            }

        } finally {
            connector.disconnect();
        }
    }

    private static void printEntry(List<Entry> entrys) {
        for (Entry entry : entrys) {
            if (entry.getEntryType() == EntryType.TRANSACTIONBEGIN || entry.getEntryType() == EntryType.TRANSACTIONEND) {
                continue;
            }

            RowChange rowChage = null;
            try {
                rowChage = RowChange.parseFrom(entry.getStoreValue());
            } catch (Exception e) {
                throw new RuntimeException("ERROR ## parser of eromanga-event has an error , data:" + entry.toString(),
                        e);
            }

            EventType eventType = rowChage.getEventType();
            System.out.println(String.format("================&gt; binlog[%s:%s] , name[%s,%s] , eventType : %s",
                    entry.getHeader().getLogfileName(), entry.getHeader().getLogfileOffset(),
                    entry.getHeader().getSchemaName(), entry.getHeader().getTableName(),
                    eventType));

            for (RowData rowData : rowChage.getRowDatasList()) {
                if (eventType == EventType.DELETE) {
                    //删除数据
                    printColumn(rowData.getBeforeColumnsList());
                } else if (eventType == EventType.INSERT) {
                    //新增数据
                    printColumn(rowData.getAfterColumnsList());
                } else {
                    //更新数据
                    System.out.println("-------&gt; before");
                    printColumn(rowData.getBeforeColumnsList());
                    System.out.println("-------&gt; after");
                    printColumn(rowData.getAfterColumnsList());
                }
            }
        }
    }

    private static void printColumn(List<Column> columns) {
        for (Column column : columns) {
            System.out.println(column.getName() + " : " + column.getValue() + "    update=" + column.getUpdated());
        }
    }
}

```
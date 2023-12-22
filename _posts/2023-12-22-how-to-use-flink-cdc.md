---
layout: post
title: flink-cdc使用
tags: backend
categories: backend
---

应用场景、上手体验



# 应用场景

Flink CDC (Change Data Capture) 是一种用于捕获和处理数据源中的变化的流处理技术。它可以实时地将数据源中的增量更新捕获到流处理作业中，使得作业可以实时响应数据变化。以下是 Flink CDC 的一些常见应用场景：

- 数据仓库和实时分析：Flink CDC 可以用于从事务型数据库或消息队列中捕获变化，并将这些变化实时地传输到数据仓库或实时分析系统中。这可以帮助实时分析、报表生成、指标计算等业务在数据更新时立即得到更新的结果，带来实时性和洞察力。

- 实时ETL和数据同步：Flink CDC 可以实时地捕获源数据变化，并将其转换成目标数据模型，然后将这些转换后的数据输送到其他系统或存储位置，实现实时ETL（Extract, Transform, Load）或数据同步的功能。这种能力可以在不中断服务的情况下对数据进行实时转换、整合和迁移。

- 反应式应用程序：Flink CDC 可以用于构建反应式应用程序，即根据数据源中的实时变化来实时响应和处理数据。这对于实时监测、告警系统、实时推荐等具有快速响应时间要求的应用非常有用。

- 数据集成和流数据处理：Flink CDC 可以捕获不同数据源中的变化，并将其转化为流数据进行实时处理。这为数据集成、变换和处理提供了一个强大的工具，使得企业可以更高效地利用和组织分散在不同数据源中的数据。

- 增量更新索引和搜索引擎：Flink CDC 可以捕获关系数据库中的变化，并将这些变化应用于搜索引擎或索引系统，以保持索引和数据的同步更新。这对于实时搜索、内容检索和全文搜索等场景非常有用。

总之，Flink CDC 提供了将数据源中的变更实时捕获到流处理作业中的能力，使得企业可以实时地处理和响应数据变化。这为实时分析、实时ETL、反应式应用程序等多个场景提供了强大的支持。

# 上手体验

## 依赖

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.airtu</groupId>
  <artifactId>airtu-cdc</artifactId>
  <version>1.0-SNAPSHOT</version>

  <properties>
    <flink-version>1.13.0</flink-version>
  </properties>

  <dependencies>

    <!--        flink connector 基础包-->
    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-connector-base</artifactId>
      <version>1.14.4</version>
    </dependency>
    <!--        CDC mysql 源-->
    <dependency>
      <groupId>com.ververica</groupId>
      <artifactId>flink-sql-connector-mysql-cdc</artifactId>
      <version>2.3.0</version>
    </dependency>
    <!--        Flink Steam流处理-->
    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-streaming-java_2.11</artifactId>
      <version>1.14.4</version>
    </dependency>
    <!--        flink java客户端-->
    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-clients_2.12</artifactId>
      <version>1.14.4</version>
    </dependency>
    <!--        开启webui支持，默认是8081，默认没有开启-->
    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-runtime-web_2.12</artifactId>
      <version>1.14.4</version>
    </dependency>
    <!--        Flink Table API和SQL API使得在Flink中进行数据处理变得更加简单和高效
    通过使用Table API和SQL API，可以像使用传统的关系型数据库一样，通过编写SQL语句或者使用类似于
    Java的API进行数据处理和分析-->
    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-table-runtime_2.11</artifactId>
      <version>1.14.4</version>
    </dependency>
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.2.11</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>2.0.6</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/cn.hutool/hutool-all -->
    <dependency>
      <groupId>cn.hutool</groupId>
      <artifactId>hutool-core</artifactId>
      <version>5.8.23</version>
    </dependency>

    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.75</version>
    </dependency>

  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>3.3.0</version>
        <configuration>
          <archive>
            <manifest>
              <addClasspath>true</addClasspath>
              <classpathPrefix>lib/</classpathPrefix>
              <mainClass>com.airtu.saas.FlinkCDC</mainClass>
            </manifest>
          </archive>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.0</version>
        <configuration>
          <source>8</source>
          <target>8</target>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <version>3.6.0</version>
        <executions>
          <execution>
            <id>copy-dependencies</id>
            <phase>package</phase>
            <goals>
              <goal>copy-dependencies</goal>
            </goals>
            <configuration>
              <outputDirectory>${project.build.directory}/lib</outputDirectory>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

</project>
```
## 定义原始数据解析器

该类是将原始的JSON数据，挑选出有用的部分组装成信息的JSON

```
package com.airtu.saas;

import com.alibaba.fastjson.JSONObject;
import com.ververica.cdc.connectors.shaded.org.apache.kafka.connect.data.Field;
import com.ververica.cdc.connectors.shaded.org.apache.kafka.connect.data.Struct;
import com.ververica.cdc.connectors.shaded.org.apache.kafka.connect.source.SourceRecord;
import com.ververica.cdc.debezium.DebeziumDeserializationSchema;
import org.apache.flink.api.common.typeinfo.BasicTypeInfo;
import org.apache.flink.api.common.typeinfo.TypeInformation;
import org.apache.flink.util.Collector;

public class CommonStringDebeziumDeserializationSchema implements DebeziumDeserializationSchema<String> {

    private String host;
    private int port;
    
    public CommonStringDebeziumDeserializationSchema(String host, int port) {
        this.host = host;
        this.port = port;
    }

    public void deserialize(SourceRecord record, Collector<String> out){

        //打印原始的数据
        System.out.println(record);

        JSONObject jsonObject = new JSONObject();
        jsonObject.put("host",host);
        jsonObject.put("port",port);
        jsonObject.put("file", record.sourceOffset().get("file"));
        jsonObject.put("pos", record.sourceOffset().get("pos"));
        jsonObject.put("ts_sec", record.sourceOffset().get("ts_sec"));

        String[] name = record.valueSchema().name().split("\\.");
        jsonObject.put("db", name[1]);
        jsonObject.put("table", name[2]);

        Struct value = ((Struct) record.value());
        String operatorType = value.getString("op");
        jsonObject.put("operator_type", operatorType);

        // insert update
        if (!"d".equals(operatorType)) {
            Struct after = value.getStruct("after");
            jsonObject.put("after", parseRecord(after));
        }
        // update & delete
        if ("u".equals(operatorType) || "d".equals(operatorType)) {
            Struct source = value.getStruct("before");
            jsonObject.put("before", parseRecord(source));
        }
        jsonObject.put("parse_time", System.currentTimeMillis() / 1000);

        out.collect(jsonObject.toString());
    }

    private JSONObject parseRecord(Struct after) {
        JSONObject jsonObject = new JSONObject();
        for (Field field : after.schema().fields()) {
            Object o = after.get(field.name());
            jsonObject.put(field.name(),o);
        }

        return jsonObject;
    }

    public TypeInformation<String> getProducedType() {
        return BasicTypeInfo.STRING_TYPE_INFO;
    }
}

```

## 测试代码

```
package com.airtu.saas;

import cn.hutool.core.date.DateTime;
import cn.hutool.core.date.DateUtil;
import com.ververica.cdc.connectors.mysql.MySqlSource;
import com.ververica.cdc.connectors.mysql.table.StartupOptions;
import com.ververica.cdc.debezium.DebeziumSourceFunction;
import org.apache.flink.configuration.Configuration;
import org.apache.flink.configuration.RestOptions;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.sink.RichSinkFunction;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URL;
import java.util.Properties;


public class FlinkCDC {

    public static void main(String[] args) throws Exception {

        //启动webui，绑定本地web-ui端口号
        Configuration configuration=new Configuration();
        configuration.setInteger(RestOptions.PORT,8081);

        //获取配置文件
        Properties properties = new Properties();
        String currentDir = System.getProperty("user.dir");
        String configFilePath = currentDir + File.separator + "application.properties";
        File file = new File(configFilePath);
        if (!file.exists()){
            ClassLoader classLoader = FlinkCDC.class.getClassLoader();
            URL resourceUrl = classLoader.getResource("application.properties");
            configFilePath = resourceUrl.getPath();
        }

        System.out.println("配置文件：" + configFilePath);
        try (FileInputStream configFile = new FileInputStream(configFilePath)) {
            properties.load(configFile);
        } catch (IOException e) {
            e.printStackTrace();
            return;
        }
        Object syncTime = properties.get("syncTime");
        DateTime dateTime = DateUtil.parse((String) syncTime, "yyyy-MM-dd HH:mm:ss");

        //获取Flink 执行环境
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment(configuration);
        env.setParallelism(1);//设置并行度为1


        //通过FlinkCDC构建SourceFunction
        DebeziumSourceFunction<String> sourceFunction = MySqlSource.<String>builder()
                .hostname("192.168.0.215")
                .port(33306)
                .username("root")
                .password("123456")
                .databaseList("test2")
                .tableList("test2.tb_user")
                .deserializer(new CommonStringDebeziumDeserializationSchema("192.168.0.215",33306)) //设置序列化器
                .startupOptions(StartupOptions.timestamp(dateTime.getTime())) //可以选择从头开始、最新的、指定时间戳位置
                .build();
        DataStreamSource<String> dataStreamSource = env.addSource(sourceFunction);

        dataStreamSource.addSink(new RichSinkFunction<String>() {
            @Override
            public void invoke(String value, Context context) throws Exception {

                //打印出重新组装的JSON数据
                System.out.println("json->: "+value);
                super.invoke(value, context);
            }
        });

        //启动任务
        env.execute("FlinkCDC");

    }

}


```

执行结果

```
SLF4J: No SLF4J providers were found.
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#noProviders for further details.
SLF4J: Class path contains SLF4J bindings targeting slf4j-api versions 1.7.x or earlier.
SLF4J: Ignoring binding found at [jar:file:/Users/applemima1234/Desktop/a/lib/logback-classic-1.2.11.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See https://www.slf4j.org/codes.html#ignoredBindings for an explanation.
配置文件：/Users/applemima1234/Desktop/a/application.properties
十二月 22, 2023 5:24:47 下午 com.github.shyiko.mysql.binlog.BinaryLogClient connect
警告: Binary log position adjusted from 0 to 4
十二月 22, 2023 5:24:47 下午 com.github.shyiko.mysql.binlog.BinaryLogClient connect
信息: Connected to 192.168.0.215:33306 at /4 (sid:5642, cid:62998)
SourceRecord{sourcePartition={server=mysql_binlog_source}, sourceOffset={transaction_id=null, ts_sec=1703228128, file=mysql-bin.000003, pos=8068, row=1, server_id=1, event=2}} ConnectRecord{topic='mysql_binlog_source.test2.tb_user', kafkaPartition=null, key=null, keySchema=null, value=Struct{after=Struct{name=oo,age=25},source=Struct{version=1.6.4.Final,connector=mysql,name=mysql_binlog_source,ts_ms=1703228128000,db=test2,table=tb_user,server_id=1,file=mysql-bin.000003,pos=8204,row=0},op=c,ts_ms=1703237088440}, valueSchema=Schema{mysql_binlog_source.test2.tb_user.Envelope:STRUCT}, timestamp=null, headers=ConnectHeaders(headers=)}
json->: {"ts_sec":1703228128,"file":"mysql-bin.000003","port":33306,"pos":8068,"parse_time":1703237088,"host":"192.168.0.215","after":{"name":"oo","age":25},"db":"test2","table":"tb_user","operator_type":"c"}
```
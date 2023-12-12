---
layout: post
title: skywalking基本使用
tags: backend
categories: backend
---

服务探针配置、服务安装



# 服务配置探针

无侵入配置

## 1.下载agent并解压

```
skywalking-agent
├── LICENSE
├── NOTICE
├── activations
├── bootstrap-plugins
├── config
├── licenses
├── logs
├── optional-plugins
├── optional-reporter-plugins
├── plugins
└── skywalking-agent.jar
```

## 2.vm参数中修改

```
-javaagent:C:\Users\zhanghuan\Desktop\practice\skywalking\skywalking-agent2\skywalking-agent.jar
-DSW_AGENT_NAME=auth
-DSW_AGENT_COLLECTOR_BACKEND_SERVICES=127.0.0.1:11800
```

注意：每个服务和agent一一对应，不能多个服务共用agent

## 3.配置日志服务

选配，配置后会将应用程序的日志通过grpc发送给OAP服务

1）依赖

```
        <dependency>
            <groupId>org.apache.skywalking</groupId>
            <artifactId>apm-toolkit-logback-1.x</artifactId>
            <version>8.5.0</version>
        </dependency>
```

2）修改logback.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <!-- 日志存放路径 -->
	<property name="log.path" value="logs/ruoyi-system" />
   <!-- 日志输出格式 -->
	<property name="log.pattern" value="%d{HH:mm:ss.SSS} [%thread] %-5level %logger{20} - [%method,%line] - %msg%n" />

    <!-- 控制台输出 -->
	<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>${log.pattern}</pattern>
		</encoder>
	</appender>

    <!-- 系统日志输出 -->
	<appender name="file_info" class="ch.qos.logback.core.rolling.RollingFileAppender">
	    <file>${log.path}/info.log</file>
        <!-- 循环政策：基于时间创建日志文件 -->
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志文件名格式 -->
			<fileNamePattern>${log.path}/info.%d{yyyy-MM-dd}.log</fileNamePattern>
			<!-- 日志最大的历史 60天 -->
			<maxHistory>60</maxHistory>
		</rollingPolicy>
		<encoder>
			<pattern>${log.pattern}</pattern>
		</encoder>
		<filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!-- 过滤的级别 -->
            <level>INFO</level>
            <!-- 匹配时的操作：接收（记录） -->
            <onMatch>ACCEPT</onMatch>
            <!-- 不匹配时的操作：拒绝（不记录） -->
            <onMismatch>DENY</onMismatch>
        </filter>
	</appender>

    <appender name="file_error" class="ch.qos.logback.core.rolling.RollingFileAppender">
	    <file>${log.path}/error.log</file>
        <!-- 循环政策：基于时间创建日志文件 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志文件名格式 -->
            <fileNamePattern>${log.path}/error.%d{yyyy-MM-dd}.log</fileNamePattern>
			<!-- 日志最大的历史 60天 -->
			<maxHistory>60</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!-- 过滤的级别 -->
            <level>ERROR</level>
			<!-- 匹配时的操作：接收（记录） -->
            <onMatch>ACCEPT</onMatch>
			<!-- 不匹配时的操作：拒绝（不记录） -->
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 发送给OAP服务  -->
    <appender name="grpc-log" class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.log.GRPCLogClientAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
                <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%X{tid}] [%thread] %-5level %logger{36} -%msg%n</Pattern>
            </layout>
        </encoder>
    </appender>

    <!-- 系统模块日志级别控制  -->
	<logger name="com.ruoyi" level="info" />
	<!-- Spring日志级别控制  -->
	<logger name="org.springframework" level="warn" />

	<root level="info">
    <!-- 发送给OAP服务  -->
        <appender-ref ref="grpc-log"/>
		<appender-ref ref="console" />
	</root>

	<!--系统操作日志-->
    <root level="info">
        <appender-ref ref="file_info" />
        <appender-ref ref="file_error" />
    </root>
</configuration>

```



# 安装服务

## 1.下载apache-skywalking-apm-bin安装包

```
apache-skywalking-apm-bin
├── LICENSE
├── LICENSE.tpl
├── NOTICE
├── README.txt
├── bin
├── config
├── config-examples
├── licenses
├── logs
├── oap-libs
├── tools
└── webapp
```

## 2.配置服务的数据库

修改apache-skywalking-apm-bin\config\application.yml

```
storage:
  selector: ${SW_STORAGE:mysql}
  mysql:
    properties:
      jdbcUrl: ${SW_JDBC_URL:"jdbc:mysql://localhost:3307/sw?rewriteBatchedStatements=true"}
      dataSource.user: ${SW_DATA_SOURCE_USER:root}
      dataSource.password: ${SW_DATA_SOURCE_PASSWORD:123456}
```

## 3.启动

运行bin目录下startup




---
layout: post
title: Tomcat内置启动
tags: java
categories: backend
---



对于一些老旧项目，不易改成jar包运行，但又要求禁用tomcat外置启动。此时，需要写一个项目调用tomcat相关的jar包，实现tomcat的功能。

1）maven配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>webservice</artifactId>
    <version>1.0</version>

    <dependencies>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-core</artifactId>
            <version>8.5.28</version>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-jasper</artifactId>
            <version>8.5.28</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>webservice</finalName>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.eclipse.m2e</groupId>
                    <artifactId>lifecycle-mapping</artifactId>
                    <version>1.0.0</version>
                    <configuration>
                        <lifecycleMappingMetadata>
                            <pluginExecutions>
                                <pluginExecution>
                                    <pluginExecutionFilter>
                                        <groupId>org.apache.maven.plugins</groupId>
                                        <artifactId>maven-dependency-plugin</artifactId>
                                        <versionRange>[2.0,)</versionRange>
                                        <goals>
                                            <goal>copy-dependencies</goal>
                                        </goals>
                                    </pluginExecutionFilter>
                                    <action>
                                        <ignore />
                                    </action>
                                </pluginExecution>
                            </pluginExecutions>
                        </lifecycleMappingMetadata>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>2.4</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                            <!-- 主方法所在类名 -->
                            <mainClass>TomcatStart</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
            <!-- 将依赖包放到lib文件夹中 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy</id>
                        <phase>package</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>
                                ${project.build.directory}/lib
                            </outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>6</source>
                    <target>6</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

2）主类

```java
import org.apache.catalina.LifecycleException;
import org.apache.catalina.core.AprLifecycleListener;
import org.apache.catalina.core.StandardContext;
import org.apache.catalina.startup.Tomcat;

import javax.servlet.ServletException;
import java.io.File;
import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;

/**
 *
 * @author Programming is an art from.
 * @Description: TODO
 */
public class TomcatStart {

    public static int TOMCAT_PORT;
    public static String TOMCAT_HOSTNAME = "127.0.0.1";
    public static String DOCBASE;
    public static String CONTEXT_PATH;


    public static void main(String[] args) throws ServletException, LifecycleException, IOException {
        //初始化
        Properties properties = new Properties();
        String configDir = System.getProperty("user.dir") + File.separator + "config.properties";
        properties.load(new FileReader(configDir));
        TomcatStart.TOMCAT_PORT = Integer.valueOf(properties.getProperty("server.port"));
        String mode = properties.getProperty("server.docbase.mode");
        if(mode.equals("relative")){
            TomcatStart.DOCBASE = System.getProperty("user.dir") + File.separator + properties.getProperty("server.docbase");
        }else {
            TomcatStart.DOCBASE = properties.getProperty("server.docbase");
        }
        String contextPath =  properties.getProperty("server.context.path");
        TomcatStart.CONTEXT_PATH = contextPath;

        //运行
        TomcatStart.run();
    }

    public static void run() throws ServletException, LifecycleException {
        Tomcat tomcat = new Tomcat();
        tomcat.setPort(TomcatStart.TOMCAT_PORT);
        tomcat.setHostname(TomcatStart.TOMCAT_HOSTNAME);
        tomcat.setBaseDir("."); // tomcat 信息保存在项目下

        StandardContext myCtx = (StandardContext) tomcat
                .addWebapp(TomcatStart.CONTEXT_PATH,DOCBASE);

        myCtx.setReloadable(false);
        // 上下文监听器
        myCtx.addLifecycleListener(new AprLifecycleListener());


        tomcat.start();
        tomcat.getServer().await();
    }

}

```

3）配置文件

```properties
server.port=13000
server.context.path=/sso
# 路径模式：relative(相对) absolute(绝对)
server.docbase.mode=relative
server.docbase=/
```

4）启动

将jar包导出放置到老项目中，找到TomcatStart的字节码文件的目录，通过`-classpath`加载到虚拟机运行，可通过脚本完成

(1)启动

```shell
#!/bin/bash

APP_NAME=com.koal.sso.client.TomcatStart
CURRENT_PATH=$(readlink -f "$(dirname "$0")")
JAVA_CMD="${JAVA_HOME}/bin/java"
JAVA_OPT="-Xms256m -Xmx256m -Xss128m "

JAVA_MAJOR_VERSION=$($JAVA -version 2>&1 | sed -E -n 's/.* version "([0-9]*).*$/\1/p')
if [[ "$JAVA_MAJOR_VERSION" -ge "9" ]] ; then
  JAVA_OPT="${JAVA_OPT}  --add-modules java.sql --illegal-access=warn"
  JAVA_OPT="${JAVA_OPT} -cp .:${KIAM_PROD_HOME}/classes"
  JAVA_OPT="${JAVA_OPT} -Xlog:gc*:file=${KIAM_PROD_LOG_DIR}/urm_gc.log:time,tags:filecount=10,filesize=102400"
else
  JAVA_OPT="${JAVA_OPT} -Djava.ext.dirs=${JAVA_HOME}/jre/lib/ext:${JAVA_HOME}/lib/ext:"
  JAVA_OPT="${JAVA_OPT} -Xloggc:${KIAM_PROD_LOG_DIR}/urm_gc.log -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCTimeStamps -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=10 -XX:GCLogFileSize=100M"
fi

# 检查JAVAHOME是否存在
env_check() {

	if [ -z $JAVA_HOME ]; then
		echo "ERR: Please set the JAVA_HOME variable in your environment."
	 	exit 1
	fi
}

# 检查程序是否在运行
is_exist(){
  pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}'`
  echo $pid
  #如果不存在返回1，存在返回0     
  if [ -z "${pid}" ]; then
   return 1
  else
    return 0
  fi
}


env_check

is_exist
if [  $? -eq 0 ];then
    echo "不可以重复运行"
    exit
fi

JAVA_OPT=${JAVA_OPT}
JAVA_OPT="${JAVA_OPT} -cp "
JAVA_OPT="${JAVA_OPT}${CURRENT_PATH}/WEB-INF/classes:"
JAVA_OPT="${JAVA_OPT}${CURRENT_PATH}/WEB-INF/lib/*:"
JAVA_OPT="${JAVA_OPT}${CURRENT_PATH}/lib/* "
JAVA_OPT="${JAVA_OPT}com.koal.sso.client.TomcatStart"

echo $JAVA_OPT
echo "运行命令"  ${JAVA_CMD}  
echo "执行jar参数为" ${JAVA_OPT}
echo "运行类名为" ${APP_NAME}
nohup $JAVA_CMD $JAVA_OPT &
echo "启动结束"
```

(2)停止

```shell
#!/bin/bash

APP_NAME=com.koal.sso.client.TomcatStart

# 检查JAVAHOME是否存在
is_exist(){
  pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}'`
  #如果不存在返回1，存在返回0     
  if [ -z "${pid}" ]; then
   return 1
  else
    return 0
  fi
}

# 获取程序pid
pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}'`

is_exist
if [  $? -eq 1 ];then
    echo "程序未在运行"
    exit
fi

kill -9 $pid
echo "已停止${pid}"

```


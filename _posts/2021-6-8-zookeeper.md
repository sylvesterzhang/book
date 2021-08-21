---
layout: post
title: zookeeper
tags: zookeeper
categories: backend
---

概述、Zookeeper设计目的、zookeepr角色介绍、工作原理、应用场景、安装、命令、JAVA操作zookeeper

**概述**

它是一个分布式服务框架，是Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。简单来说zookeeper=文件系统+监听通知机制。 

1）文件系统 

Zookeeper维护一个类似文件系统的数据结构，树的每个节点可存放数据

2）监听机制

客户端注册监听它关心的目录节点，当目录节点发生变化（数据改变、被删除、子目录节点增加删除）时，zookeeper会通知客户端。

---

 **Zookeeper设计目的**

1.最终一致性：client不论连接到哪个Server，展示给它都是同一个视图，这是zookeeper最重要的性能。 

2.可靠性：具有简单、健壮、良好的性能，如果消息被到一台服务器接受，那么它将被所有的服务器接受。 

3.实时性：Zookeeper保证客户端将在一个时间间隔范围内获得服务器的更新信息，或者服务器失效的信息。但由于网络延时等原因，Zookeeper不能保证两个客户端能同时得到刚更新的数据，如果需要最新数据，应该在读数据之前调用sync()接口。 

4.等待无关（wait-free）：慢的或者失效的client不得干预快速的client的请求，使得每个client都能有效的等待。 

5.原子性：更新只能成功或者失败，没有中间状态。 

6.顺序性：包括全局有序和偏序两种：全局有序是指如果在一台服务器上消息a在消息b前发布，则在所有Server上消息a都将在消息b前被发布；偏序是指如果一个消息b在消息a后被同一个发送者发布，a必将排在b前面。

---

**zookeepr角色介绍**

**领导者（leader）**，负责进行投票的发起和决议，更新系统状态（数据同步），发送心跳。

**学习者（learner）**，包括跟随者（follower）和观察者（observer）。

1) **跟随者（follower）**，用于接受客户端请求、向客户端返回结果，在选主过程中参与投票。

2)  **观察者（Observer）**，可以接受客户端请求，会把请求转发给leader，**但observer不参加投票过程，只同步leader的状态**，observer的目的是为了扩展系统，提高读取速度。

---

**工作原理** 

Zookeeper的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议。**Zab协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。**

1）恢复模式：当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，恢复模式不接受客户端请求，当领导者被选举出来，且大多数Server完成了和leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和Server具有相同的系统状态。

2）广播模式：一旦Leader已经和多数的Follower进行了状态同步后，他就可以开始广播消息了，即进入广播状态。这时候当一个Server加入ZooKeeper服务中，它会在恢复模式下启动，发现Leader，并和Leader进行状态同步。待到同步结束，它也参与消息广播。ZooKeeper的广播状态一直到Leader崩溃了或者Leader失去了大部分的Followers支持。

---

**应用场景**

1）数据发布与订阅

发布与订阅即所谓的配置管理，顾名思义就是将数据发布到ZK节点上，供订阅者动态获取数据，实现配置信息的集中式管理和动态更新。

2）分布式通知/协调

不同的系统都监听同一个节点，一旦有了更新，另一个系统能够收到通知

3）分布式锁

Zookeeper能保证数据的强一致性，用户任何时候都可以相信集群中每个节点的数据都是相同的。锁的两种体现方式：

(1）保持独占

一个用户创建一个节点作为锁，另一个用户检测该节点，如果存在，代表别的用户已经锁住，如果不存在，则可以创建一个节点，代表拥有一个锁。

(2）控制时序

有一个节点作为父节点，其底下是带有编号的子节点，所有要获取锁的用户，需要在父节点下创建带有编号的子节点，编号最小的会持有锁；当最小编号的节点被删除后，锁被释放，再重新找最小编号的节点来持有锁，这样保证了全局有序。

4）集群管理

每个加入集群的机器都创建一个节点，写入自己的状态。监控父节点的用户会收到通知，进行相应的处理。离开时删除节点，监控父节点的用户同样会收到通知。

---

**安装**

1）下载

```
cd /usr/local
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.7.0/apache-zookeeper-3.7.0-bin.tar.gz  
tar -zxvf zookeeper-3.4.12.tar.gz
cd zookeeper-3.4.12
```

2）重命名配置文件

```
cp conf/zoo_sample.cfg conf/zoo.cfg
```

3）启动

```
bin/zkServer.sh start
```

4）客户端连接

```
bin/zkCli.sh
```

5）看日志

```
zkServer.sh start-foreground
```

如果启动不成功，日志中显示端口被暂用，可将zoo.cfg客户端端口进行修改，并关闭adminServer

```
clientPort=40008
admin.enableServer=false
```



---

**常见命令**

| 命令               | 含义                   | 执行后反馈             |
| ------------------ | ---------------------- | ---------------------- |
| ls /               | 查看当前节点下的子节点 |                        |
| create -s /zk1 123 | 创建顺序节点           | Created /zka0000000001 |
| create -e /zk2 123 | 创建临时节点           | Created /zka2          |
| create  /zk2 123   | 创建持久节点           | Created /zk2           |
| get /zk10000000000 | 查看当前节点的数据     | 123                    |
| set /zk2 222       | 更新节点               |                        |
| delete /zk2 222    | 删除节点               |                        |
| ls -s /            | 查看节点详细           |                        |
| quit               | 退出                   |                        |
|                    |                        |                        |

---

**JAVA操作zookeeper**

```java
package com.example.demo;

import org.I0Itec.zkclient.IZkDataListener;
import org.I0Itec.zkclient.ZkClient;
import org.I0Itec.zkclient.exception.ZkMarshallingError;
import org.I0Itec.zkclient.serialize.ZkSerializer;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

import javax.annotation.PreDestroy;
import java.nio.charset.Charset;

public class zk {
    static ZkClient client;
    @BeforeAll
    public static void createClient(){
        client = new ZkClient("192.168.50.33:2181");
        client.setZkSerializer(new ZkSerializer() {
            @Override
            public byte[] serialize(Object data) throws ZkMarshallingError {
                return String.valueOf(data).getBytes(Charset.defaultCharset());
            }

            @Override
            public Object deserialize(byte[] bytes) throws ZkMarshallingError {
                return new String(bytes,Charset.defaultCharset());
            }
        });
    }
    @Test
    public void test1(){
        //创建节点,但不可重复创建
        if(!client.exists("/name")){
            client.createPersistent("/name","zhanghuan");
        }else{
            System.out.println("节点已存在");
        }
    }
    @Test
    public void test2(){
        //删除节点，即使不存在也不报错
        client.delete("/name");
        //递归删除节点
        client.deleteRecursive("/a/b/c");
    }
    @Test
    public void test3(){
        //更新节点
        client.writeData("/name","1121");
    }
    @Test
    public void test4(){
        //获取节点
        System.out.println((String) client.readData("/name"));
    }
    @Test
    public void test5() throws InterruptedException {
        //订阅
        client.subscribeDataChanges("/name", new IZkDataListener() {
            @Override
            public void handleDataChange(String dataPath, Object data) throws Exception {
                System.out.println("数据已更新");
            }

            @Override
            public void handleDataDeleted(String dataPath) throws Exception {
                System.out.println("数据已删除");
            }
        });
        Thread.sleep(Integer.MAX_VALUE);
    }
    @PreDestroy
    public void closeClient(){
        client.close();
    }
}

```


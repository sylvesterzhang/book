---
layout: post
title: MySQL高级
tags: database
categories: backends
---

数据库引擎对比、索引、SQL语句的性能分析、优化、其他

**数据库引擎对比**

|        | MyISAM     | InnoDB           |
| ------ | ---------- | ---------------- |
| 外键   | 不支持     | 支持             |
| 事务   | 不支持     | 支持             |
| 行表锁 | 表锁       | 行锁             |
| 缓存   | 只缓存索引 | 索引、数据都缓存 |
| 表空间 | 小         | 大               |
| 关注点 | 性能       | 事务             |

---

**索引**

`1`索引分类

单值索引：一个索引只包含单个列

复合索引：一个索引包含多个列

唯一索引：索引值唯一，单允许有空值

`2`索引相关操作

1）创建

```
create index index_name on table_name(column);
```

2）删除

```
drop index index_name on table_name;
```

3）查看

```
show index from table_name;
```

`3`创建索引适合的情况和不适合的情况

1）适合情况

(1)表的主键，创建表后会自动创建索引

(2)频繁作为查询条件的字段

(3)与其他表相关联的字段，如外键所在的字段

2）不适合情况

索引能时查询速度和排序速度增快，但也会使更新速度变慢，以下情况不适合创建索引

(1)频繁更新的字段

(2)where后的条件用不到的字段

(3)数据量小的表

(4)重复项较多的字段，比如性别

`3`索引下推

一张表有一个联合索引(name,age)，执行以下语句

```
select * from user where name like "zhang%" and age<=10;
```

对于这个语句有两种执行方式

1）联合索引查询所有满足“zhang”开头的索引，再回表中查询出满足年龄小于等于10的数据

2）联合索引查询所有满足“zhang”开头的索引，再继续筛选出年龄小于等于10的索引，最后再返回表中查询全行数据（这种方式称为索引下推）

Mysql默认开启索引下推

---

**SQL语句的性能分析**

1）查看SQL语句的性能

```
explain SQL
```

2）查询性能结果中的字段含义分析

| 字段          | 可能出现值                                               | 含义                                                         |
| ------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| id            | 数字                                                     | 指向顺序，相同表示从上至下执行，不同表示从下到上执行(数值大的先执行) |
| select_type   | simple,primary,devived,union,union_result                | 查询的类型                                                   |
| type          | 扫描类型值                                               | 扫描类型，与性能相关                                         |
| possible_keys | 索引名                                                   | 可能涉及到的索引，查询涉及到的字段上若存在索引，则索引被列出，但查询过程中不一定使用 |
| key           | 索引名                                                   | 实际使用的索引，若为null表示未使用索引                       |
| key_len       | 数字                                                     | 索引的大小                                                   |
| rows          | 数字                                                     | 找到记录所读取的行数                                         |
| extra         | useing filesort,using temporarymusing index,coving index | 额外信息                                                     |

3）type对应扫描类型值对应含义

| 扫描类型 | 含义                                                     |
| -------- | -------------------------------------------------------- |
| const    | 索引扫描，一次找到                                       |
| eq-ref   | 唯一索引扫描，对于每个索引键，表中有唯一一条记录与之匹配 |
| ref      | 非唯一索引扫描，返回某个单独值的所有行                   |
| range    | 给定范围扫描                                             |
| index    | 所有扫描                                                 |
| all      | 全表扫描                                                 |

一般来说，该值达到range或ref级别即可

---

**优化**

`1`Mysql性能达瓶颈原因

1）cpu饱和

2）需装入的数据远远大于内存容量

3）硬件性能瓶颈

`2`Mysql优化建议

1）对于复合索引，想让它生效，在语句WHERE后条件的第一个字段需用到复合索引的第一个字段

2）对于复合索引，想让它效率更高，在语句WHERE后条件中的字段不跳过过索引中的列

3）查询条件中对应的索引列，不要做任何操作如计算cacl等

4）对于复合索引，在语句WHERE后条件中有范围的条件，之后其他关联索引的字段全部失效

5）查询内容尽量覆盖索引，减少使用select *（导致索引失效，占用过多sort_buffer，效率降低）

6）不用不等于，会导致全表扫描

7）查询null或not null，无法使用索引

8）like后的%，尽量加到右侧，否则导致索引失效（利用覆盖索引可解决）

9）在vachar类型字段作为查询条件时，一定要加上引号，否则可能导致索引失效，同时也可能造成行锁变成表锁

10）少用or，连接时导致索引失效

`3`项目中优化SQL思路

1）慢查询的开启与捕获

2）explain慢SQL分析

3）show profile查询SQL在myql服务器里执行细节和生命周期情况

4）SQL数据库服务器的参数优化

---

**常见的锁**

`1`共享锁

读锁，可查看但无法修改和删除的数据锁。若事务对数据加上共享锁后，其他事务只能对该数据加共享锁

`2`排他锁

写锁、独占锁。若事务对数据对象加上该锁，其他数据不能对该数据再加任何类型的锁，直至该事务释放该锁

`3`互斥锁

编程中引入的概念，来保证共享数据操作的完整性，每个锁都有一个互斥锁的标记，以保证任一时刻，只有一个线程访问该对象

`4`悲观锁

假设最坏的情况，每次在拿到数据的时候都会上锁。只有拿到锁后才可对数据进行操作，如传统关系性数据库用到的行锁、表锁，或如java里的同步原语sychronized关键字的实现

`5`乐观锁

每次拿数据时认为别人不会修改，所以不会上锁。但更新时会判断此期间别人有没有去更新数据，如版本号机制实现。乐观锁适用于多读的应用类型，像数据库提供的类似于write_condition机制或如java.util.conCurrent.atomic包下的原子变量类就是乐观锁的一种实现方式（cas）

`6`行级锁

Mysql中粒度最小的一种锁，只针对当前行进行加锁。分为共享锁和排他锁。优点是冲突少，并发高；缺点是开销大，会出现死锁

`7`表级锁

Mysql中粒度最大的一种锁，实现简单，资源消耗小。分为共享锁和独占写锁。优点是开销小，不会出现死锁；缺点是发生冲突概率高

`8`页级锁

粒度介于行级锁和表级锁之间，会出现死锁



---

**其他**

1）exists查询

主查询的数据，放到子查询中做条件验证，根据验证结果（true，false）决定查询结果是否保留（小表驱动大表）

```
select * from A where exists(select 1 from B where B.id=A.id);
```

相当于

```
select * from A where id in (select id from B);
```

2）Mysql排序方式

FileSort和Index，其中Index效率更高。当索引失效时，会用FileSort进行排序

3）间隙锁

SQL查询条件是一个范围时，InnoDb会给满足这一条件的数据记录加锁，这些在条件范围内不存在的记录称为间隙，而此时形成的锁称为间隙锁

4）锁住一行

```
select * from table where id=9 for update;
```


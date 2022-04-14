---
layout: post
title: Oracle总结
tags: database
categories: database
---

概述、卸载、SID-数据库名-服务名、配置文件、PDB和CDB、创建用户、SQL plus登录方式

# 概述

实例是操作系统中访问数据库所需要的一系列的进程和内存的集合。即使没有任何数据文件，实例也可以启动。但是要想访问数据库，必须把数据库文件加载进实例中。实例和数据库的区别可以简单概括为：实例是临时的，它只在相关的进程和内存集合存在时存在，而数据库是永久的，只要文件存在它就存在。一个实例只能对应一个数据库，但是一个数据库可以由多个实例对应（如RAC）。

Oracle数据库软件：Oracle服务器

Oracle实例：运行在Oracle服务器中的服务，接受并响应客户端请求

Oracle数据库：运行在Oracle数据库实例中为客户端提供数据存储服务

# 卸载

1）如果数据库配置了自动存储管理（ASM），先删除聚集同步服务，DOS指令：localconfig delete

2）关闭所有Oracle服务

3）在安装目录下找到deinstall.bat双击执行卸载

4）删除自动存储管理（ASM），DOS指令：oracle-deleete-asnsid+asm

5）删除注册表

KEY_LOCAL_MACHINE/SOFTWARE/ORACLE目录

HKEY_LOCAL_MACHINE/SYSTEEM/CurrentControlSet/Services中所有以oracle或OraWeb开头的键

HKEY_CURRENT_USER/Software/Microsoft/Windows/CurrentVersion/Explorer/MenuOrder/Start Mnue/Programs中所有哦以oracle开头的键

KEY_LOCAL_MACHINE/SOFTWAR/ODBC/ODBCINST.INI中除Microsoft ODBC for Oracle注册表键以外所有含有Oracle的键

6）删除环境变量中的PATH和CLASSPATH中包含Oracle的值

7）删除开始/程序中所欲Oracle的组和图标

8）删除所有和ORACLE相关的目录



# SID、数据库名、服务名

1）SID

SYSTEM IDENNTIFER 系统识别号，实例名。在单实例的时候，实例名和数据库名是一样的。在多实例的时候，一个SID对应一个实例名，多个实例名对应一个数据库名。

2）数据库名

DATABASE AME 在单实例环境下，数据库就是实例名。在多实例环境下，数据库名是唯一的，实例名会有多个

3）服务名

SERVICE NAME 默认情况下，数据库名、SID就是服务名。但一个数据库是可以设置多个服务名的



# 配置文件

1）说明

位置在\product\12c\dbhome_1\NETWORK\ADMIN

listener.ora

配置服务监听器，可在这里配置服务监听的端口

sqlnet.ora

名称解析，通过这个文件来决定怎么样找一个连接中出现的连接字符串

tnsnames.ora

用在oracle client端，用户配置连接数据库的别名参数，就像系统中的hosts文件一样

2）配置监听

打开Oracle Net Configuration Assistant，点击监听程序配置，完成配置

3）配置网络服务

首先关闭防火墙，打开Oracle Net Configuration Assistant，点击本地网络服务名配置，配置好后在tnsnames.ora中看到新增的字符

```
NETORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.137.129)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = orcl)
    )
  )
```



# PDB和CDB

Oracle 12C引入了CDB与PDB的新特性，在ORACLE 12C数据库引入的多租用户环境（Multitenant Environment）中，允许一个数据库容器（CDB）承载多个可插拔数据库（PDB）。CDB全称为Container Database，中文翻译为数据库容器，PDB全称为Pluggable Database，即可插拔数据库。在ORACLE 12C之前，实例与数据库是一对一或多对一关系（RAC）：即一个实例只能与一个数据库相关联，数据库可以被多个实例所加载。而实例与数据库不可能是一对多的关系。当进入ORACLE 12C后，实例与数据库可以是一对多的关系。

1）查看当前容器

```
SQL> show con_name;

CON_NAME
------------------------------
CDB$ROOT
```

2）查看CDB

```
SQL> select name,cdb from v$database;

NAME      CDB
--------- ---
ORCL      YES
```

3）切换根容器  

```
SQL> alter session set container=CDB$ROOT;

会话已更改。
```

```
conn / as sysdba
```

4）关闭CDB

```
connect sys@orcl as sysdba
shutdown immediate
```

5）查看实例

```
SQL> select instance_name,status from v$instance;

INSTANCE_NAME    STATUS
---------------- ------------
orcl             OPEN
```

6）打开PDB（内部）

```
alter database open;
```

7）关闭PDB（根容器）

```
alter pluggable database pdb1 close immediate;
```

8）删除PDB（根容器）

```
alter pluggable database pdb1 close;
drop pluggable database pdb1 [including datafiles];
```

9）查看当前有哪些PDB

```
SQL> show pdbs;

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 PDBORCL                        MOUNTED
```

10）查看数据库文件

```
SQL> select tablespace_name,file_name from dba_data_files;

TABLESPACE_NAME
------------------------------
FILE_NAME
--------------------------------------------------------------------------------
USERS
C:\APP\ORACLE\ORADATA\ORCL\USERS01.DBF

UNDOTBS1
C:\APP\ORACLE\ORADATA\ORCL\UNDOTBS01.DBF

SYSAUX
C:\APP\ORACLE\ORADATA\ORCL\SYSAUX01.DBF


TABLESPACE_NAME
------------------------------
FILE_NAME
--------------------------------------------------------------------------------
SYSTEM
```

11）创建表空间并指定为默认表空间

```
SQL> create tablespace zhanghuan datafile 'C:\app\ORACLE\oradata\orcl\pdborcl\ZHANGHUAN01.DBF' size 50m;

表空间已创建。

SQL> alter pluggable database default tablespace zhanghuan;

插接式数据库已变更。
```

```
create tablespace 永久表空间名称 datafile '永久表空间物理文件位置.DBF' size 15M autoextnd on next 10M permanent online;
```

12）查看所有表空间

```
select * from dba_tablespaces;
```



# 创建用户

1）用户分类

普通用户：创建PDB里

通用用户：创建在CDB里

2）分配角色(Role privileges)

```
connect
```

可以直接设置成dba角色，就不用分配后面的权限了

界面分配系统权限(System privileges)

```
create any table
create any view
create any sequencee 
create any synonym
create any index
unlimited tabelspace
```

3）创建用户并授权

```
create user c##zhanghuan identified by 123456 default tablespace zhanghuan;
grant dba to c##zhanghuan;
```

4）账号解锁

```
alter user system account unlock;
```

5）修改密码

```
alter system identified by password;
```



# SQL plus登录方式

1）本地管理员登录

```
请输入用户名：sys as sysdba
输入口令：
连接到：
Oracle Database 12c Relieace 12.1.0.1.0 -64bit Production
```

2）网络用户登录

```
system/pwd
```

3）通过容器名连接

```
sys/Aa12345678@orcl as sysdba
```

4）通过网络服务名连接

```
sys/Aa12345678@192.168.137.129/orcl as sysdba
```



ORACLE中表名大小写不敏感




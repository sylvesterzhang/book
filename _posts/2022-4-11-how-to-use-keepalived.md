---
layout: post
title: keepalived总结
tags: linux
categories: linux
---

基本操作、修改配置文件、开始

# 基本操作

1）安装

```shell
yum install keepalived -y
```

2）配置文件

```
/etc/keepalived/keepalived.conf
```

3）日志文件

```
/var/log/messages
```

4）卸载

```shell
yum remove keepalived
```



# 修改配置文件

```shell
vi /etc/keepalived/keepalived.conf
```

执行`set nu`，再将光标移动到35行，按dG，删除掉35行以后的内容

```
global_defs {
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   router_id LVS_DEVEL
   vrrp_skip_check_adv_addr
   # vrrp_strict 这一行注释掉
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

```



```
vrrp_instance VI_1 实例组名 {
    state MASTER 角色名(MASTER/BACKUP)
    interface eth0 网卡名称
    virtual_router_id 51 组编号，服务器之间亚要保持一致
    priority 100 权重，用于选举
    advert_int 1
    authentication {
        auth_type PASS 授权类型
        auth_pass 1111 组密码，服务器间密码要保持一致
    }
    virtual_ipaddress {
        10.0.1.100 虚拟的ip地址，集群中的机器要保持一致
   }
}
```

# 开始

1）关闭NetworkManager

```shell
systemctl disable NetworkManager
systemctl stop NetworkManager
```

2）启动

```shell
systemctl start keepalived
```

执行`ip a`检查是否生效

3）停止

```shll
pkill keepalived
```

4）报错

无法启动，日志中出现`(VI_1): Cannot find an IP address to use for interface ens33`

```shll
ifconfig eth1 172.16.20.101/16 up
```

